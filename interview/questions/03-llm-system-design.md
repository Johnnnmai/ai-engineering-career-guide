# LLM System Design Questions

Format: 45-60 min whiteboard or virtual whiteboard discussion. The interviewer expects you to drive the conversation, ask clarifying questions, make tradeoffs explicit, and demonstrate end-to-end thinking from requirements through scaling. I found that most candidates fail not on technical depth but on structuring their answer. Use a framework.


## Framework for LLM System Design

I use a six-step framework that works for any LLM system design question. Spend 2-3 minutes on each step, adjusting depth based on interviewer cues.

Step 1: Requirements (3-5 min)
Clarify functional requirements (what the system does), non-functional requirements (latency, throughput, cost, accuracy), and constraints (budget, team size, existing infrastructure). Ask who the users are, what volume to expect, and what failure looks like.

Step 2: High-level architecture (5-7 min)
Draw the main components and data flow. For most LLM systems this includes: user interface, API gateway, orchestration layer, model serving layer, data/knowledge layer, monitoring layer.

Step 3: Component deep dive (15-20 min)
Go deep on the most interesting or challenging components. This is where you demonstrate technical depth.

Step 4: Model selection and prompt engineering (5 min)
Which model(s) to use and why. Prompt design, few-shot examples, system prompts.

Step 5: Evaluation and quality (5 min)
How do you measure success? What metrics? How do you detect regression?

Step 6: Scaling and operations (5 min)
How does this scale to 10x traffic? What breaks first? Cost projections. [^chip-huyen-2022] [^oreilly-llm-eng-2024]


## Design Question 1: Customer Support Chatbot

"Design an AI-powered customer support system for an e-commerce company handling 10K conversations per day."

This is the most common LLM system design question. I have seen it at Amazon, Salesforce, Zendesk, and multiple startups.

Requirements to clarify:
- Do we need to handle multiple languages?
- Can the bot take actions (refund, cancel order) or only provide information?
- What is the acceptable hallucination rate? (For support, near zero on policy questions.)
- What is the target resolution rate without human handoff?
- What is the latency budget? (Users expect < 3 seconds for first token.)

Architecture:

```
User -> API Gateway -> Conversation Orchestrator -> [Intent Classifier]
                                                  -> [RAG Pipeline] -> Knowledge Base
                                                  -> [Action Engine] -> Order System
                                                  -> [Safety Layer] -> Content Filter
                                                  -> [Handoff Logic] -> Human Agent
```

Key components:

Intent classification: Use a lightweight classifier (fine-tuned BERT or even regex + keyword matching for common intents) to route conversations. Not everything needs the LLM. "Where is my order?" can be answered by a database lookup without LLM involvement.

RAG for knowledge: Index the company's help articles, policy documents, and FAQ into a vector database. Use hybrid search (BM25 + semantic) with a reranker. Chunk at the section level with metadata (category, last_updated). This ensures answers are grounded in actual policy rather than hallucinated. [^lewis-rag-2020]

Action engine: Define a set of tools the LLM can invoke (check_order_status, initiate_refund, update_address). Use structured function calling to ensure the LLM provides the right parameters. Require confirmation for destructive actions (refunds, cancellations). Log every action for audit.

Safety layer: Input filtering for prompt injection attempts, PII detection and redaction (do not send customer SSNs to the LLM), output validation to ensure responses do not contradict company policy. A second LLM call as a "judge" to check for policy violations is worth the extra cost and latency for high-stakes support.

Human handoff: Detect when the bot is uncertain (low confidence, repeated user frustration, explicit request for human) and hand off to a human agent with full conversation context. Track handoff rate as a key metric.

Evaluation:
- Resolution rate (% of conversations resolved without human handoff)
- Customer satisfaction (CSAT survey post-conversation)
- Hallucination rate (sample 100 conversations daily, have humans verify factual claims)
- Average handle time
- Cost per conversation (LLM API costs + infrastructure)

Scaling:
- Cache common query-response pairs (FAQ-style questions)
- Use a smaller, faster model (Haiku-class) for simple intents, larger model (Sonnet-class) for complex ones
- Rate limit per user to prevent abuse
- Horizontal scaling of the orchestration layer is straightforward since conversations are independent [^zendesk-ai-2024]


## Design Question 2: AI Code Review System

"Design an AI system that automatically reviews pull requests for a large engineering organization."

I have seen versions of this at Google, GitHub, and several developer tool startups.

Requirements to clarify:
- What languages? All, or a specific stack?
- Should it block merges or only suggest?
- What is the false positive tolerance? (Engineers ignore noisy tools.)
- Integration with existing CI/CD?
- Access to the full repo or just the diff?

Architecture:

```
Git Push -> Webhook -> PR Analyzer -> [Diff Parser]
                                   -> [Context Builder] -> Repo Index
                                   -> [Review Engine] -> LLM(s)
                                   -> [Comment Formatter] -> GitHub API
                                   -> [Learning Loop] -> Feedback DB
```

Key design decisions:

Context window management: A PR diff alone is insufficient context. The review engine needs to understand what the changed functions do, their callers, related tests, and coding conventions. Build a code index of the repository (tree-sitter for parsing, embeddings for semantic search) and retrieve relevant context for each changed file. Budget your context window: diff (30%), retrieved context (50%), instructions and examples (20%). [^openai-codex-2021]

Multi-pass review: First pass with a fast model to categorize the PR (bug fix, feature, refactor, test) and identify obvious issues (unused imports, formatting). Second pass with a stronger model on the most critical files for logic errors, security vulnerabilities, and design concerns. This keeps cost manageable while maintaining quality.

False positive management: Developers dismissing a comment is a strong negative signal. Track the dismiss rate per rule category and automatically suppress categories with > 30% dismiss rate until they are retrained. Let developers upvote/downvote comments and feed this back into the system.

Repo-specific conventions: Fine-tune or few-shot the model with examples of approved PRs from the team. Extract coding conventions from existing linting rules and past review comments. The system should learn that "this team uses dependency injection" or "this team prefers explicit error handling over exceptions."

Security scanning: Use structured analysis tools (Semgrep, CodeQL) for known vulnerability patterns rather than relying on the LLM. LLMs are good at contextual reasoning ("this function handles user input but does not sanitize it") but bad at exhaustive pattern matching.


## Design Question 3: Document Summarization Service

"Design a system that summarizes long documents (legal contracts, research papers, earnings reports) for a financial services firm."

Requirements to clarify:
- Document length range? (10 pages to 500 pages)
- Required accuracy level? (Financial: very high, errors are costly)
- Latency tolerance? (30 seconds for a 10-page doc? 5 minutes for 500 pages?)
- Should it extract specific fields or provide free-form summaries?
- Compliance requirements? (Can data leave the network?)

Architecture:

```
Document Upload -> [Document Parser] -> [Chunker] -> [Summarization Pipeline]
                                                   -> [Entity Extractor]
                                                   -> [Cross-Reference Checker]
                                                   -> [Output Formatter]
                                                   -> Document Store
```

Key components:

Document parsing: Convert PDF/DOCX to clean text preserving structure (headings, tables, lists). This is harder than it sounds. PDF tables are notoriously difficult. Use a combination of `pdfplumber` for text and `tabula` for tables. For scanned documents, add an OCR step (Tesseract or a document AI model). [^pdfplumber-docs]

Chunking strategy: For legal documents, chunk at the clause/section level, not by token count. Each chunk should be a semantically complete unit. Use document structure (headings, numbered sections) to guide chunking. Maintain a hierarchy: section -> subsection -> clause. This is critical because a summarization system that splits a clause across chunks will produce incoherent summaries.

Hierarchical summarization for long documents: Summarize each section independently, then summarize the section summaries. Map-reduce pattern. For a 200-page legal contract: chunk into sections (~50 sections), summarize each section to 200-300 words, then summarize all section summaries into a final 1-2 page executive summary. This handles documents far longer than any model's context window. [^wu-recursive-summarization-2021]

Faithfulness verification: For financial documents, a hallucinated number can cause real damage. After generating the summary, run a verification step: extract all numbers, dates, and named entities from the summary and verify each appears in the source document. Flag any claim that cannot be traced back to a specific source passage. This is expensive (doubles LLM calls) but necessary for high-stakes domains.

Field extraction: Use structured output (JSON mode or function calling) to extract specific fields: parties, dates, amounts, obligations, termination clauses. Define a schema per document type. Validate extracted fields against expected formats and ranges.


## Design Question 4: Multi-Modal Search Engine

"Design a search engine that can find relevant results across text documents, images, and videos for an internal knowledge base."

Architecture:

```
Query -> [Query Understanding] -> [Multi-Modal Router]
                                -> Text Search (BM25 + Semantic)
                                -> Image Search (CLIP embeddings)
                                -> Video Search (frame sampling + transcription)
                                -> [Result Fusion] -> [Reranker] -> Results
```

Key design decisions:

Unified embedding space vs. separate: CLIP-style models encode text and images in the same embedding space, allowing cross-modal search (text query finds relevant images). For text-to-text and image-to-image, dedicated models perform better. The practical approach is to use CLIP for cross-modal queries and specialized models (e5, ColBERT for text; DINOv2 for images) for within-modality search, then fuse results. [^radford-clip-2021]

Video handling: Videos are expensive to index. Options: (1) extract keyframes at regular intervals (every 5 seconds) and index as images, (2) transcribe audio and index as text, (3) both. For most enterprise use cases, transcription + keyframe extraction covers 90% of search needs. Use a chunked approach: each 30-second segment becomes a searchable unit with a timestamp for deep linking.

Result fusion: When results come from multiple modalities, how do you rank them? Reciprocal rank fusion (RRF) is a simple baseline: score = sum(1 / (k + rank)) across all sources. For better results, train a cross-modal reranker that takes the query and candidate results from all modalities and produces a unified ranking. [^cormack-rrf-2009]

Indexing pipeline: Build an async pipeline that processes new documents as they are uploaded. Text: extract, chunk, embed, index. Images: generate captions (BLIP-2 or LLaVA), extract CLIP embeddings, index both. Videos: extract keyframes, transcribe audio, chunk transcription, embed and index everything. Store raw files in object storage (S3), metadata and embeddings in a vector database.


## Design Question 5: AI Writing Assistant

"Design a writing assistant that helps users draft, edit, and improve their writing in real-time."

Key components:

Streaming architecture: Users expect character-by-character streaming, not waiting 10 seconds for a complete response. Use server-sent events (SSE) from the API gateway to the client. The model generates tokens which flow through a safety filter and are streamed to the user. Cancel inflight requests if the user starts typing again.

Suggestion modes: Autocomplete (predict the next few words), rewrite (improve a selected passage), expand (elaborate on a point), summarize (condense), tone adjustment (make more formal/casual). Each mode maps to a different prompt template. Some modes (autocomplete) require very low latency (< 500ms) and should use a small, fast model. Others (full rewrite) can tolerate 2-3 seconds and benefit from a larger model.

Context management: The assistant needs context beyond the current paragraph: the full document, the user's writing style, the target audience. For long documents, use a sliding window of the most relevant sections plus a compressed summary of the full document. Cache the document embedding and update incrementally as the user edits.

Personalization: Learn each user's writing style over time. Store a style profile (vocabulary complexity, sentence length distribution, common phrases, preferred tone) and inject it into the system prompt. Fine-tuning per user is too expensive; few-shot examples of their best writing in the prompt is practical.

Privacy: Writing assistants see everything the user writes, including sensitive content. Process data in-region, do not log user content beyond the current session (unless explicitly opted in), and offer an on-device mode for sensitive documents. This is a product-market-fit issue as much as a technical one. [^notion-ai-2023]


## Key Components Across All Designs

Model selection matrix:
- High throughput, low latency, simple tasks: Small model (Haiku-class, 8B-parameter open source)
- Moderate complexity, good quality: Medium model (Sonnet-class, 70B-parameter)
- Complex reasoning, highest quality: Large model (Opus-class, frontier)
- Use the smallest model that meets quality requirements for each subtask

Caching strategies:
- Exact match cache: Hash the prompt, return cached response for identical prompts (saves 90%+ on FAQ-style workloads)
- Semantic cache: Embed the prompt, return cached response for similar prompts (riskier, needs similarity threshold tuning)
- KV cache: Reuse computed attention states for shared prefixes (system prompt + few-shot examples)
- Cache invalidation: Time-based expiry, version-based invalidation when underlying data changes

Rate limiting:
- Per-user rate limits to prevent abuse
- Per-model rate limits to stay within provider quotas
- Token-based limits (not just request-based) since a 100K token request costs 100x a 1K token request
- Graceful degradation: when approaching limits, route to smaller models or return cached responses

Cost optimization:
- Prompt compression (remove redundant context, use shorter system prompts)
- Response length limits (max_tokens appropriate to the task)
- Batch processing for non-real-time workloads
- Model routing: cheap model first, escalate to expensive model only when needed
- Monitor cost per query and set alerts [^chen-frugalgpt-2023]

Latency budget breakdown (typical for a RAG chatbot):
- Network + API gateway: 50ms
- Embedding query: 50ms
- Vector search: 50ms
- Context retrieval and assembly: 100ms
- LLM generation (time to first token): 200-500ms
- LLM generation (full response): 1-5 seconds
- Safety check: 100ms
- Total (first token): ~500ms, Total (complete): ~2-6 seconds


## Guardrails and Safety

Every LLM system design answer should include a safety section. Interviewers increasingly expect this.

Input filtering:
- Prompt injection detection (classifier trained on known injection patterns)
- PII detection and redaction before sending to external LLM APIs
- Content policy enforcement (block prohibited topics)
- Input length and rate validation

Output validation:
- Factual grounding check (does every claim in the output have a source?)
- Policy compliance check (does the output violate company policies?)
- Format validation (is the output valid JSON, proper markdown, within length limits?)
- Toxicity and bias detection

Defense in depth:
- System prompt hardening (clear boundaries on what the model should and should not do)
- Output parsing rather than raw text execution (never eval() LLM output)
- Human-in-the-loop for high-stakes decisions
- Audit logging of all inputs, outputs, and intermediate steps [^anthropic-safety-2024]


## Monitoring and Observability

Quality metrics:
- Task-specific accuracy (RAG: answer correctness; summarization: faithfulness)
- Hallucination rate (sampled and manually verified)
- User satisfaction (thumbs up/down, CSAT surveys)
- Failure rate (errors, timeouts, safety filter triggers)

Operational metrics:
- Latency percentiles (p50, p95, p99) for each component
- Token usage and cost per request
- Cache hit rate
- Model provider availability and error rates

Drift detection:
- Monitor embedding distribution of incoming queries over time
- Compare weekly query clusters to detect topic drift
- Track model performance on a fixed evaluation set (regression detection)
- Alert on sudden changes in any metric [^mlops-community-2024]


## Company-Specific Patterns

OpenAI/Anthropic tend to ask about model serving infrastructure, safety systems, and evaluation frameworks. They assume deep LLM knowledge and focus on systems at scale.

Google asks about search and retrieval at massive scale, multi-modal systems, and infrastructure efficiency. They expect discussion of distributed systems primitives.

Meta focuses on recommendation systems, social features (content moderation, feed ranking), and experimentation infrastructure (A/B testing at scale).

Amazon asks about customer-facing AI (Alexa, product recommendations, customer support) and emphasizes operational excellence, cost optimization, and Working Backwards from the customer. Expect questions about how to handle failure modes.

Startups tend to ask more open-ended questions ("Design our product from scratch") and care more about speed of iteration, cost efficiency with limited resources, and choosing the right abstraction level (build vs. use an API). [^levels-fyi-ai-2024]


## How to Prepare

Practice 10 designs. For each, write out the full framework (requirements, architecture, components, evaluation, scaling) in 30 minutes. Then present it to a friend or record yourself presenting it in 45 minutes. Review the recording for clarity and completeness.

Study real architectures. Read engineering blog posts from companies building LLM products: Notion AI architecture, Cursor's code completion system, Perplexity's search pipeline, ChatGPT's infrastructure. These give you concrete examples to reference in your answers.

Build a small system end-to-end. Even a simple RAG chatbot that you deploy and serve real traffic to teaches you more about system design tradeoffs than reading 10 papers.

Keep a "components catalog" of patterns you can mix and match: caching layer, safety filter, evaluation pipeline, model router, streaming endpoint, feedback loop. Most LLM system designs combine the same 10-15 components in different configurations.


## Common Mistakes

Diving into implementation without clarifying requirements. The first five minutes should be all questions. Interviewers are testing whether you can identify what matters before you start building.

Ignoring cost. LLM systems are expensive. If your design uses GPT-4 for every request at 10K requests per day, your monthly API bill is $30K-100K+. Show that you think about cost by routing to cheaper models, caching, and batching.

Forgetting safety and evaluation. A system without guardrails and monitoring is not production-ready. Even if the interviewer does not explicitly ask, bring it up.

Over-engineering for hypothetical scale. If the requirement is 10K requests per day, you do not need Kubernetes autoscaling and multi-region deployment. Design for the actual requirements, then briefly discuss how you would scale if needed.

Not making tradeoffs explicit. "We could use a larger model for better quality, but that increases latency from 2s to 5s and triples cost. Given our latency requirement of 3s, I would start with the medium model and upgrade only for queries the medium model handles poorly." This shows engineering judgment.

Treating the LLM as a black box. Show that you understand what happens inside: tokenization affects cost, context window limits affect architecture, temperature affects consistency, and these have concrete system design implications.


## Sources

[^chip-huyen-2022]: Chip Huyen, "Designing Machine Learning Systems," O'Reilly 2022
[^oreilly-llm-eng-2024]: Various authors, "LLM Engineering" blog series, O'Reilly 2024
[^lewis-rag-2020]: Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks," NeurIPS 2020
[^zendesk-ai-2024]: Zendesk Engineering Blog, "Building AI-Powered Customer Support" 2024
[^openai-codex-2021]: Chen et al., "Evaluating Large Language Models Trained on Code," OpenAI 2021
[^wu-recursive-summarization-2021]: Wu et al., "Recursively Summarizing Books with Human Feedback," OpenAI 2021
[^pdfplumber-docs]: pdfplumber documentation, https://github.com/jsvine/pdfplumber
[^radford-clip-2021]: Radford et al., "Learning Transferable Visual Models From Natural Language Supervision," ICML 2021
[^cormack-rrf-2009]: Cormack, Clarke, Buettcher, "Reciprocal Rank Fusion outperforms Condorcet and individual Rank Learning Methods," SIGIR 2009
[^notion-ai-2023]: Notion Engineering Blog, "How We Built Notion AI" 2023
[^chen-frugalgpt-2023]: Chen et al., "FrugalGPT: How to Use Large Language Models While Reducing Cost and Improving Performance," 2023
[^anthropic-safety-2024]: Anthropic, "Claude's Character and Safety Design" 2024
[^mlops-community-2024]: MLOps Community, "LLMOps: Operationalizing Large Language Models" 2024
[^levels-fyi-ai-2024]: Levels.fyi, "AI Engineer Interview Guide" 2024
