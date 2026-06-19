# RAG and Agent Architecture Questions

Format: 45-60 min deep dive, usually with a senior engineer or tech lead who has built these systems. This is where AI Engineer interviews diverge most sharply from general software engineering interviews. The interviewer expects you to have built at least one RAG system or agent and to speak from experience, not just theory. I found that interviewers probe hardest on failure modes and evaluation, because these are the areas where reading tutorials diverges from production reality.


## RAG Fundamentals

Q: Walk me through how you would design a RAG pipeline from scratch.

A production RAG pipeline has five stages: ingestion, chunking, embedding, retrieval, and generation.

Ingestion: Parse source documents (PDF, HTML, DOCX, Markdown) into clean text. This step is deceptively hard. PDF parsing loses table structure, DOCX has hidden formatting, HTML has boilerplate. Use `unstructured` or `docling` for multi-format parsing. Store the raw documents alongside parsed text so you can re-parse when parsers improve.

Chunking: Split parsed text into retrieval units. The chunk size determines the granularity of retrieval. Too small (100 tokens) and you lose context. Too large (2000 tokens) and you dilute relevance. I typically start with 500-token chunks with 50-token overlap and adjust based on evaluation.

Embedding: Convert each chunk into a dense vector using an embedding model. `text-embedding-3-large` (OpenAI), `e5-large-v2`, or `bge-large-en` are strong choices. The embedding model determines the semantic space your retrieval operates in, and changing it requires re-embedding everything.

Retrieval: Given a query, embed it and find the nearest chunks in vector space. Use a vector database (Pinecone, Weaviate, Qdrant, pgvector) or a library (FAISS) depending on scale and operational requirements. Return the top-k chunks (typically k=5-10).

Generation: Construct a prompt with the retrieved chunks as context and the user's question. The LLM generates an answer grounded in the provided context. Include instructions to cite sources and say "I don't know" when the context does not contain the answer. [^lewis-rag-2020]

Follow-up: What are the failure modes of this basic pipeline?

At least six that I have encountered in production. (1) Bad parsing: the PDF parser drops a table, so the system cannot answer questions about data in that table. (2) Wrong chunk boundaries: a concept spans two chunks and neither chunk alone has enough context. (3) Embedding mismatch: the query uses different terminology than the document ("terminate the agreement" vs. "cancel the contract"). (4) Retrieval miss: relevant content exists but is not in the top-k results. (5) Distractor chunks: irrelevant but superficially similar chunks are retrieved, confusing the LLM. (6) LLM hallucination: the LLM generates plausible-sounding information not present in the retrieved context. [^barnett-rag-survey-2024]


## Chunking Strategies

Q: Compare different chunking strategies and when you would use each.

Fixed-size chunking: Split every N tokens with M token overlap. Simple, predictable, works for homogeneous documents. Poor for structured documents where logical boundaries matter.

Recursive character splitting: Split by paragraphs first, then sentences, then characters until chunks are within the size limit. Respects natural text boundaries better than fixed-size. This is LangChain's default and a reasonable starting point.

Semantic chunking: Use sentence embeddings to detect topic shifts. Group consecutive sentences that are semantically similar into chunks. Better boundary detection but slower and more complex. Greg Kamradt's approach uses cosine similarity between consecutive sentence embeddings and splits at low-similarity boundaries. [^kamradt-semantic-chunking-2023]

Document-structure chunking: Use headings, sections, and other structural elements as chunk boundaries. Best for well-structured documents (technical documentation, legal contracts, academic papers). Requires a parser that preserves structure (not just raw text extraction).

Parent-child chunking: Index small chunks for precise retrieval but return the larger parent chunk (or the full section) as context. This gives you the precision of small chunks with the context of large chunks. LlamaIndex calls this "sentence window retrieval."

In practice, I start with recursive character splitting at 500 tokens with 50 overlap, evaluate retrieval quality, and switch to semantic or structural chunking only if evaluation shows boundary problems causing retrieval failures. [^llamaindex-chunking-2024]


## Advanced RAG

Q: What is hybrid search and why does it outperform pure semantic search?

Hybrid search combines keyword-based search (BM25) with semantic search (dense vector) and merges the results. BM25 catches exact keyword matches that semantic search misses (product IDs, error codes, specific technical terms, proper nouns). Semantic search catches conceptual matches that BM25 misses (synonyms, paraphrases, implicit relationships). Neither alone covers all query types. The standard approach is to run both searches independently, normalize scores to [0,1], and combine with weighted sum: `score = alpha * bm25_score + (1 - alpha) * semantic_score`. Alpha around 0.3-0.5 works well as a starting point. Reciprocal rank fusion (RRF) is an alternative that avoids score normalization: `rrf_score = sum(1 / (k + rank_i))`. [^ma-hybrid-search-2024]

Q: Explain reranking and when it adds value.

A reranker takes the initial retrieval results (say top-50 from hybrid search) and re-scores them using a more expensive, more accurate model. Cross-encoder rerankers (like Cohere Rerank or `bge-reranker-large`) process the query and each candidate document together, computing a relevance score that captures fine-grained semantic interactions that bi-encoder embeddings miss. The tradeoff is latency: cross-encoders are 100x slower than bi-encoders per document, which is why you rerank a small candidate set rather than the full corpus. Reranking consistently improves retrieval quality by 5-15% on standard benchmarks. It is worth the added latency (typically 100-200ms for reranking 50 candidates) in most production systems. [^nogueira-reranking-2020]

Q: What is query expansion and how does it help retrieval?

Query expansion generates additional search queries from the original user query to improve recall. Three approaches:

LLM-based expansion: Ask the LLM to generate 3-5 alternative phrasings of the query. "What are the side effects of aspirin?" becomes ["aspirin adverse reactions", "aspirin risks", "problems with taking aspirin"]. Run retrieval for each expanded query and merge results. This is sometimes called "multi-query retrieval." [^langchain-multi-query-2023]

Hypothetical Document Embeddings (HyDE): Ask the LLM to generate a hypothetical answer to the query, embed that answer, and use it as the search query. The intuition is that a hypothetical answer is closer in embedding space to real answers than the question itself is. Works well for questions where the query phrasing is very different from the document phrasing. [^gao-hyde-2023]

Step-back prompting: For complex queries, ask the LLM to generate a more general question. "Why did the Q3 2024 revenue decline in the APAC region?" steps back to "What were the Q3 2024 financial results by region?" This retrieves broader context that may contain the specific answer. [^zheng-stepback-2024]


## RAG Evaluation

Q: How do you evaluate a RAG system?

Evaluate retrieval and generation separately, then end-to-end.

Retrieval metrics:
- Recall@k: Of all relevant documents, what fraction is in the top-k results? This is the most important retrieval metric. If relevant content is not retrieved, the LLM cannot use it.
- Precision@k: Of the top-k results, what fraction is relevant? Low precision means you are stuffing irrelevant context into the LLM prompt, wasting tokens and potentially confusing it.
- MRR (Mean Reciprocal Rank): Average of 1/rank of the first relevant result. Measures how quickly users find what they need.
- NDCG (Normalized Discounted Cumulative Gain): Considers the position of all relevant documents, not just the first. Useful when there are multiple relevant documents at different relevance levels. [^manning-ir-2008]

Generation metrics:
- Faithfulness: Does the generated answer contain only information from the retrieved context? (No hallucination)
- Answer relevance: Does the answer address the user's question?
- Context relevance: Is the retrieved context relevant to the question?

Evaluation frameworks: RAGAS is the most widely used automated evaluation framework for RAG. It computes faithfulness, answer relevance, and context relevance using LLM-as-judge. However, I found that LLM judges have significant biases (they prefer longer, more detailed answers even when brevity is appropriate) and should be complemented with human evaluation on a random sample. [^es-ragas-2024]

Building an evaluation dataset: Start with 50-100 question-answer pairs where you manually identify the relevant source documents. Use this as your golden set. Expand over time by logging real user queries and having humans annotate relevance. Never optimize solely on automated metrics without checking against human judgment.

Q: What recall@k value should I target?

It depends on the use case. For customer support (wrong answer = unhappy customer), target recall@10 above 0.95. For internal knowledge search (users can browse multiple results), recall@10 above 0.85 is acceptable. For legal or medical applications (missing information can be dangerous), target recall@20 above 0.99 and use multiple retrieval strategies. The key insight is that recall at the retrieval stage is a ceiling on end-to-end accuracy. If the relevant document is not retrieved, no amount of prompt engineering will fix it.


## Agent Architecture

Q: Explain the ReAct pattern for LLM agents.

ReAct (Reasoning + Acting) interleaves chain-of-thought reasoning with tool use. The agent follows a loop: Thought (reason about what to do next), Action (invoke a tool), Observation (read the tool's output), repeat until it can produce a final answer. This is more robust than pure chain-of-thought (which has no access to external information) or pure acting (which has no explicit reasoning). The prompt typically includes few-shot examples showing the Thought/Action/Observation format. Most modern agent frameworks (LangChain, LlamaIndex, CrewAI) implement variants of ReAct. [^yao-react-2023]

Example flow:
```
User: What was Apple's revenue in Q3 2024?
Thought: I need to search for Apple's Q3 2024 financial results.
Action: search("Apple Q3 2024 revenue earnings report")
Observation: Apple reported Q3 2024 revenue of $85.8 billion...
Thought: I found the answer. Let me verify by checking the original source.
Action: fetch_url("https://investor.apple.com/...")
Observation: [quarterly results page content]
Thought: Both sources confirm $85.8 billion. I can answer now.
Final Answer: Apple's revenue in Q3 2024 was $85.8 billion.
```

Q: What are the main failure modes of LLM agents?

I have encountered these repeatedly in production:

Tool selection errors: The agent picks the wrong tool or uses correct tools with wrong parameters. Mitigation: clear tool descriptions, parameter validation, and limiting the number of available tools (agents with 20+ tools make frequent selection errors).

Infinite loops: The agent retries the same failed action or oscillates between two actions. Mitigation: max iteration limits, loop detection (if the same action+parameters repeat, break), and exponential backoff on failures.

Hallucinated tool calls: The agent invokes tools that do not exist or fabricates observations without actually calling the tool. Mitigation: strict tool schema validation, verify that observations come from actual tool execution.

Context window exhaustion: Multi-step agents accumulate observations that eventually exceed the context window, causing the agent to "forget" early steps. Mitigation: summarize intermediate steps, only keep the most recent observations in full, compress older history.

Premature termination: The agent declares it has the answer before completing all necessary steps. Mitigation: require the agent to verify its answer against the evidence, use a verification step before final output.

Cost explosion: A poorly controlled agent can make dozens of LLM calls per user query. Mitigation: budget limits (max N tool calls, max M tokens total), cost tracking per agent run, alerts on expensive runs. [^wang-agent-survey-2024]


## Multi-Agent Systems

Q: When would you use multiple agents instead of a single agent?

Multiple agents are justified when: (1) the task requires different expertise that does not fit well into a single prompt (e.g., a research agent, a writing agent, and a fact-checking agent), (2) you need parallel execution (multiple agents can search different sources simultaneously), (3) you want separation of concerns for safety (a planning agent proposes actions, a separate execution agent carries them out, a safety agent reviews). Multiple agents add complexity: inter-agent communication, shared state management, error propagation, and debugging difficulty. I found that single-agent with multiple tools handles 80% of use cases. Use multi-agent only when single-agent quality is measurably insufficient.

Q: Describe common orchestration patterns for multi-agent systems.

Sequential pipeline: Agent A produces output, Agent B refines it, Agent C verifies it. Simple, predictable, but slow and inflexible. Good for content generation pipelines (draft -> edit -> fact-check).

Router pattern: A lightweight orchestrator agent determines which specialist agent should handle each query. Similar to a microservices gateway. Good when different query types require fundamentally different approaches.

Debate pattern: Multiple agents independently answer the same question, then a judge agent evaluates and synthesizes the best answer. Increases quality but multiplies cost. Good for high-stakes decisions where reliability matters more than cost.

Hierarchical delegation: A manager agent breaks a task into subtasks and delegates each to worker agents. The manager synthesizes results. Good for complex, multi-step tasks (research reports, code generation with testing).

Consensus pattern: Multiple agents vote on each decision. The majority vote determines the action. More robust than single-agent but slower and more expensive. Good for critical classification tasks (content moderation, medical triage). [^wu-autogen-2023]


## Real Design Questions

Q: Design a RAG system for legal document search.

This is a high-stakes RAG application where missing a relevant document can have legal consequences.

Key design decisions:

Chunking must respect legal document structure. Chunk at the clause level, not arbitrary token boundaries. A clause is the atomic unit of legal meaning. Use structural parsing (numbered sections, defined terms) to identify clause boundaries. Maintain cross-references: when clause 3.2 references "as defined in Section 1.1," the retrieval system should also fetch Section 1.1.

Retrieval must prioritize recall over precision. Use hybrid search with heavy BM25 weighting (legal searches often use precise legal terms). Set k=20-30 (more than typical RAG). Add a reranker trained or fine-tuned on legal text. Include metadata filtering: jurisdiction, document type, date range, parties.

Generation must be ultra-conservative. The LLM should cite specific clause numbers. It should distinguish between "the contract says X" (direct quote) and "the contract implies X" (inference). Include a confidence indicator. Never generate legal advice; frame outputs as "the document states" not "you should."

Evaluation requires legal domain experts. Standard RAGAS metrics are insufficient. Have lawyers evaluate a sample of queries weekly, checking both retrieval completeness and answer accuracy. Track "near misses" (relevant documents that were ranked 11th-20th but should have been in top 10). [^trautmann-legal-nlp-2022]

Q: Build an agent that can book travel.

This requires tool integration, multi-step planning, and user preference management.

Tools needed: flight search API, hotel search API, calendar access (check availability), payment processing, email (send confirmations), user preference store (seat preference, hotel chain loyalty, budget).

Agent flow: (1) Parse the user's request ("Book a trip to Tokyo next week, budget $3000"). (2) Check calendar for available dates. (3) Search flights within budget, filtered by preferences. (4) Present options and get user confirmation. (5) Search hotels for confirmed dates, near the user's specified area or interests. (6) Present options and get confirmation. (7) Execute bookings. (8) Send confirmation email with itinerary.

Critical design decisions: Never auto-book without explicit user confirmation (this is a one-way door with real money). Handle partial failures gracefully (flight booked but hotel unavailable). Maintain conversation state across multiple interactions (the user might come back hours later to modify the itinerary). Implement price monitoring (notify if prices change between search and booking).

Q: Design a code generation agent with self-correction.

Architecture: (1) Generate code based on the user's request. (2) Write tests for the generated code. (3) Execute the code and tests in a sandboxed environment. (4) If tests fail, analyze the error, modify the code, and retry. (5) Repeat until tests pass or max iterations reached.

Sandboxing is critical: execute generated code in a container with no network access, limited file system, CPU/memory limits, and a timeout. Never execute LLM-generated code on the host system.

Self-correction loop: Parse the error message, include it in the next prompt along with the failed code, and ask the model to fix the specific issue. I found that LLMs fix simple errors (syntax, off-by-one, wrong API call) reliably but struggle with algorithmic errors (wrong approach entirely). If the same error persists after 2 attempts, switch strategies: regenerate from scratch with a different prompt rather than iterating on broken code.

Test generation: Generate tests before the implementation code (TDD-style). This gives the agent a concrete target and makes the self-correction loop meaningful. The agent should generate both passing cases and edge cases. [^chen-humaneval-2021]


## Production Concerns

Q: How do you manage costs in a production RAG or agent system?

I track cost at three levels:

Per-component: embedding cost per document (one-time), retrieval cost per query (compute + database), LLM cost per generation (tokens in + tokens out). Most cost is in the LLM generation step.

Per-query: Total cost for a single user interaction. For a simple RAG query: ~$0.01-0.05. For a multi-step agent: ~$0.10-2.00. Set per-query budgets and alert on outliers.

Monthly aggregate: Project total monthly cost at current query volume. Model it as: monthly_cost = queries_per_day * 30 * avg_cost_per_query. A system handling 10K queries/day at $0.05/query costs $15K/month.

Cost reduction strategies: Cache frequent queries (can cut LLM calls by 30-50% for FAQ-heavy workloads). Use a smaller model for simple queries and route to a larger model only for complex ones. Reduce context size (retrieve fewer chunks, compress retrieved text). Batch embedding operations. Use prompt caching (Anthropic and OpenAI offer this) for shared system prompts.

Q: How do you handle latency in production?

Latency budget for a RAG system: embedding the query (50ms) + vector search (30-100ms) + reranking (100-200ms) + LLM generation time-to-first-token (200-500ms) + full generation (1-5s). Total p50 around 2-3 seconds, p99 around 6-8 seconds.

Optimization: pre-compute and cache embeddings, use approximate nearest neighbor search instead of exact, stream the LLM response so users see output immediately, run retrieval and safety checks in parallel where possible, use a smaller model for the reranking step.

Q: How do you handle errors and fallbacks?

Tiered fallback: (1) If the primary retrieval returns no results, fall back to broader search (remove filters, expand query). (2) If the LLM fails (timeout, rate limit), retry with exponential backoff, then fall back to a secondary model. (3) If the secondary model also fails, return a graceful error message with the retrieved documents so the user can read them directly. (4) Never return an empty response. Always return something useful, even if it is "I could not find a definitive answer, but here are the most relevant documents." Log every fallback for monitoring. [^mlops-patterns-2024]


## How to Prepare

Build a complete RAG system end-to-end. Use a real corpus (company documentation, Wikipedia subset, legal documents). Implement hybrid search, a reranker, and streaming generation. Evaluate with RAGAS and a manually curated test set of 50 questions. Measure retrieval recall, answer faithfulness, and latency. This single project covers 70% of what interviewers ask.

Build a simple agent with 3-4 tools. Implement the ReAct loop from scratch (do not just use LangChain). Add error handling, max iteration limits, and cost tracking. Run it on 20 diverse queries and analyze the failure modes. This teaches you more than any tutorial.

Study the papers. Read the original RAG paper (Lewis et al. 2020), the ReAct paper (Yao et al. 2023), and the HyDE paper (Gao et al. 2023). Skim the survey papers for breadth.

Practice explaining tradeoffs. "Why did you choose chunk size 500? What happens if you increase it to 1000? When would fixed-size chunking be better than semantic chunking?" Be ready to justify every design decision with specific reasoning, not just "best practice."


## Common Mistakes

Describing RAG as simple retrieval + generation without discussing failure modes. Every interviewer who has built a RAG system knows about chunking problems, retrieval misses, and hallucination despite context. If you describe RAG as a clean pipeline without failure modes, you signal that you have not built one in production.

Over-engineering the architecture. If the interviewer asks for a RAG system and you start describing a multi-agent system with eight specialized agents, you are solving a harder problem than asked. Start simple, build the basic pipeline, then layer on complexity only when you can articulate what problem each addition solves.

Not knowing evaluation metrics. If you cannot explain recall@k, MRR, and NDCG, or if you cannot describe how to evaluate faithfulness and hallucination, you will not pass this round. Evaluation is what separates "I followed a tutorial" from "I shipped a production system."

Ignoring cost in agent design. An agent that makes 50 LLM calls per user query costs $1-5 per query. If you do not discuss cost management, budget limits, and model routing, the interviewer will question whether you can build production systems.

Treating agents as magic. Agents fail frequently and unpredictably. If your design does not include error handling, fallback strategies, max iteration limits, and human escalation paths, it is not production-ready.


## Sources

[^lewis-rag-2020]: Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks," NeurIPS 2020
[^barnett-rag-survey-2024]: Barnett et al., "Seven Failure Points When Engineering a Retrieval Augmented Generation System," arXiv 2024
[^kamradt-semantic-chunking-2023]: Greg Kamradt, "5 Levels of Text Splitting," YouTube/GitHub 2023
[^llamaindex-chunking-2024]: LlamaIndex Documentation, "Chunking Strategies" 2024
[^ma-hybrid-search-2024]: Ma et al., "Hybrid Search is All You Need," arXiv 2024
[^nogueira-reranking-2020]: Nogueira et al., "Document Ranking with a Pretrained Sequence-to-Sequence Model," EMNLP 2020
[^langchain-multi-query-2023]: LangChain Documentation, "MultiQueryRetriever" 2023
[^gao-hyde-2023]: Gao et al., "Precise Zero-Shot Dense Retrieval without Relevance Labels," ACL 2023
[^zheng-stepback-2024]: Zheng et al., "Take a Step Back: Evoking Reasoning via Abstraction," ICLR 2024
[^manning-ir-2008]: Manning, Raghavan, Schutze, "Introduction to Information Retrieval," Cambridge 2008
[^es-ragas-2024]: Es et al., "RAGAS: Automated Evaluation of Retrieval Augmented Generation," EACL 2024
[^yao-react-2023]: Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models," ICLR 2023
[^wang-agent-survey-2024]: Wang et al., "A Survey on Large Language Model based Autonomous Agents," arXiv 2024
[^wu-autogen-2023]: Wu et al., "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation," arXiv 2023
[^trautmann-legal-nlp-2022]: Trautmann et al., "Legal NLP Meets MRC," EMNLP 2022
[^chen-humaneval-2021]: Chen et al., "Evaluating Large Language Models Trained on Code," OpenAI 2021
[^mlops-patterns-2024]: Kreuzberger et al., "Machine Learning Operations (MLOps): Overview, Definition, and Architecture," IEEE Access 2024
