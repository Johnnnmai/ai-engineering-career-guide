# AI Engineer Take-Home Projects

Take-home assignments are increasingly common in AI Engineer interviews, especially at startups and mid-stage companies. They replace or supplement whiteboard rounds. Typical time limit is 4-8 hours (though companies claim "3-4 hours," budget double). I found that the quality bar is higher than you expect because you are competing against candidates who spend 20+ hours on a "4-hour" assignment. The differentiator is not just working code but clean code, thoughtful documentation, evaluation metrics, and a clear explanation of design decisions.


## Category 1: RAG System Implementation (Most Common, 45%+)

This is the single most common take-home assignment for AI Engineer roles. Roughly half of all take-homes I have seen or heard about involve building some form of RAG system.

Typical prompt: "Build a chatbot that can answer questions about [a given corpus of documents]. The system should retrieve relevant information and generate accurate answers with citations."

What the corpus looks like varies: company documentation, a set of research papers, product manuals, legal contracts, or a custom dataset provided as PDFs/Markdown files.

Real assignment example: "Given this set of 50 technical blog posts about our product, build a RAG-powered Q&A system. Users should be able to ask natural language questions and receive answers grounded in the blog content. Include source citations in your responses. You have 6 hours." [^gh-rag-take-home-2024]

What evaluators look for (in order of importance):

1. Does it work? Can you demo it answering 5-10 questions correctly with citations? This is pass/fail. If the system does not produce reasonable answers, nothing else matters.

2. Retrieval quality. Did you implement chunking thoughtfully (not just splitting every 500 characters)? Did you consider overlap? Did you try hybrid search or just pure vector search? Did you include a reranker?

3. Answer quality. Are answers grounded in the retrieved context? Do they include citations? Does the system say "I don't know" when the answer is not in the corpus?

4. Code quality. Is the code organized, readable, and documented? Can someone else run it from the README? Are there type hints? Is there error handling?

5. Evaluation. Did you build any evaluation? Even a simple set of 10 test questions with expected answers shows you think about quality systematically.

6. Design decisions documented. Why did you choose this chunk size? This embedding model? This vector database? A short `DECISIONS.md` file that explains your reasoning demonstrates engineering maturity.

Recommended architecture for take-home RAG:

```
documents/ -> [parser] -> [chunker] -> [embedder] -> [vector store]
                                                          |
user query -> [embedder] -> [retriever] -> [reranker] -> [prompt builder] -> [LLM] -> answer
```

Technology choices that work well for take-homes:
- Vector store: ChromaDB (zero setup, in-memory, good for demos) or FAISS
- Embedding: `text-embedding-3-small` (cheap) or `e5-base-v2` (free, local)
- LLM: Claude or GPT-4o (best quality for demo), or Llama 3 via Ollama (shows you can work with open source)
- Framework: LlamaIndex or LangChain are fine. Raw implementation without a framework is also fine and can impress if done cleanly.
- Interface: Streamlit or Gradio for a quick UI. A CLI is also acceptable. FastAPI endpoint if they specified "API." [^streamlit-docs] [^gradio-docs]

Project structure that evaluators appreciate:

```
project/
  README.md           # setup, run instructions, design decisions
  requirements.txt    # or pyproject.toml
  ingest.py           # document parsing and indexing
  retrieve.py         # retrieval logic
  generate.py         # LLM generation with prompt template
  app.py              # main application (CLI or web)
  evaluate.py         # evaluation script with test questions
  data/               # provided corpus
  tests/              # basic tests
  DECISIONS.md        # why you made each design choice
```


## Category 2: LLM Application Development (30%+)

These assignments ask you to build a specific AI-powered feature or tool, not just a generic chatbot.

Real assignment examples:

"Build an AI-powered code reviewer that takes a GitHub PR URL, analyzes the diff, and produces a structured review with specific feedback on code quality, bugs, and suggestions. Time: 8 hours." [^gh-code-reviewer-assignment]

"Build a tool that takes a product requirements document (PRD) and generates user stories in the format: As a [user], I want [feature], so that [benefit]. The tool should identify edge cases and suggest acceptance criteria. Time: 6 hours." [^gh-prd-processor]

"Create an AI agent that can interact with a SQLite database: accept natural language questions, generate SQL queries, execute them, and return human-readable answers. Include error handling for malformed queries. Time: 4 hours." [^gh-text-to-sql]

What evaluators look for:

1. Prompt engineering quality. Is the system prompt well-structured? Does it use few-shot examples? Is the output format specified clearly? Are edge cases handled in the prompt?

2. Error handling. What happens when the LLM returns malformed output? When the API times out? When the input is empty or malformed? Production-quality error handling is a strong differentiator.

3. Structured output. Do you parse LLM output reliably? Using JSON mode, function calling, or robust parsing with fallbacks? Regex-based parsing of free-form text is fragile and evaluators notice.

4. Streaming. If the assignment involves a user-facing tool, implementing streaming responses shows production awareness.

5. Cost consciousness. If you process a large input, do you chunk it efficiently? Do you use the appropriate model size for each subtask? Mentioning cost estimates in your README shows engineering maturity.


## Category 3: ML Pipeline Design (20%+)

These assignments focus on the data-to-prediction pipeline, including data processing, feature engineering, model selection, and evaluation.

Real assignment examples:

"Given this dataset of customer support tickets (10K rows, text + metadata), build a classification system that categorizes tickets by urgency (low/medium/high) and routes them to the appropriate team. Evaluate your system and document your approach. Time: 8 hours." [^gh-ticket-classifier]

"Build a pipeline that takes raw text reviews and predicts a star rating (1-5). Compare at least two approaches (e.g., fine-tuned classifier vs. LLM-based) and discuss tradeoffs. Time: 6 hours." [^gh-sentiment-pipeline]

What evaluators look for:

1. Data exploration. Did you look at the data before building? Distribution of classes, missing values, text length distribution, outliers. A few visualization plots in a notebook show you did not just throw data at a model blindly.

2. Baseline first. Start with the simplest approach (TF-IDF + logistic regression, or zero-shot LLM classification) before building anything complex. If the simple approach achieves 90% accuracy, a complex approach that achieves 92% needs to justify its additional complexity.

3. Evaluation rigor. Train/test split (not random if there is temporal structure). Cross-validation if the dataset is small. Confusion matrix, per-class precision/recall, not just overall accuracy. If classes are imbalanced, discuss how you handled it.

4. Comparison. If you try multiple approaches, compare them fairly on the same test set with the same metrics. A table showing "Approach A: 85% accuracy, 50ms latency, $0/query" vs "Approach B: 91% accuracy, 2s latency, $0.01/query" is exactly what evaluators want to see.

5. Production readiness discussion. Even if you do not build the full pipeline, discuss: How would you retrain? How would you monitor for drift? How would you handle new categories that emerge after deployment?


## Category 4: Agent Development (15%+)

These assignments ask you to build an agent that performs multi-step tasks using tools.

Real assignment examples:

"Build an agent that can research a topic by searching the web, reading articles, and producing a structured summary with citations. The agent should be able to handle follow-up questions. Time: 8 hours." [^gh-research-agent]

"Create an agent that helps users manage their personal finances. It should be able to categorize transactions from a CSV, answer questions about spending patterns, and generate monthly reports. Time: 6 hours." [^gh-finance-agent]

What evaluators look for:

1. Tool design. Are the tools well-defined with clear descriptions, input schemas, and output formats? Can the LLM reliably select and use them?

2. Conversation management. Does the agent maintain context across turns? Can it reference previous results? Does it handle conversation history efficiently (summarization, sliding window)?

3. Error recovery. What happens when a tool fails? Does the agent retry, try an alternative approach, or inform the user? How does it handle ambiguous user input?

4. Safety. Does the agent validate inputs before executing tools? Can it be prompt-injected into performing unauthorized actions? Are destructive operations (deleting data, sending emails) gated behind confirmation?

5. Observability. Can you see what the agent is doing? Logging of thought process, tool calls, and intermediate results. A simple trace log is sufficient for a take-home.


## Category 5: Model Evaluation (10%+)

These assignments give you a model and dataset and ask you to evaluate, analyze, and improve performance.

Real assignment examples:

"We provide a fine-tuned model and a test dataset of 500 question-answer pairs. Evaluate the model's performance, identify systematic failure patterns, and propose (and ideally implement) improvements. Time: 6 hours." [^gh-model-eval]

"Given access to our production chatbot and a set of 200 user conversations with satisfaction ratings, analyze what makes conversations succeed or fail. Recommend specific improvements. Time: 4 hours." [^gh-chatbot-analysis]

What evaluators look for:

1. Systematic analysis. Not just overall metrics but breakdown by category, input length, question type, topic. Where does the model fail consistently?

2. Error taxonomy. Categorize failures: hallucination, incomplete answers, refusals, wrong format, factually correct but unhelpful. Each category suggests different improvements.

3. Actionable recommendations. "Use a better model" is useless. "Add few-shot examples for questions about pricing, which account for 30% of failures" is actionable.

4. Quantified impact. "Fixing the top 3 failure categories would improve accuracy from 78% to an estimated 89%, based on manual review of 50 failure cases."


## Evaluation Criteria Found in Real Assignments

I collected these from assignment instructions and evaluator rubrics shared by companies and candidates:

Technical quality (40% of evaluation):
- Code correctness and functionality
- Appropriate technology choices
- Error handling and edge cases
- Performance and efficiency

Engineering practices (25%):
- Code organization and readability
- Documentation (README, inline comments, docstrings)
- Testing (even minimal tests show good habits)
- Reproducibility (can someone else run your code from the README?)

Design thinking (20%):
- Architecture decisions and justifications
- Tradeoff analysis (accuracy vs. latency vs. cost)
- Evaluation methodology
- Awareness of production considerations

Communication (15%):
- Clear explanation of approach
- Honest discussion of limitations
- Thoughtful response to "what would you do with more time?"
- Presentation quality (if a Loom video or writeup is required) [^hiring-rubric-2024]


## How to Prepare

Build one project per category. You do not need five polished portfolio projects, but you should have built at least one RAG system, one LLM application, and one evaluation pipeline end-to-end. These become your starting templates that you can adapt to specific assignment requirements.

Create a reusable starter template. I keep a template repo with: FastAPI boilerplate, ChromaDB setup, LLM API wrapper with retry logic, Streamlit frontend skeleton, basic evaluation framework, and a README template. When I get a take-home, I clone this template and customize it, saving 1-2 hours of boilerplate setup.

Practice under time pressure. Set a 6-hour timer and build a RAG system from scratch. Do this twice. The first time you will run over. The second time you will know where to cut corners and where to invest time.

Prepare a Loom video script. Many companies ask for a 5-10 minute walkthrough video. Structure it as: (1) Demo the working system (2 min), (2) Explain architecture and key decisions (3 min), (3) Show evaluation results (2 min), (4) Discuss limitations and next steps (2 min). Record a practice video and time it.

Best practices that differentiate strong submissions:

Include a `DECISIONS.md` file. For each major decision (model choice, chunk size, framework, database), write 2-3 sentences explaining why. This demonstrates you made deliberate choices, not random ones.

Include evaluation results. Even if the assignment does not explicitly ask for evaluation, include a table of results on 10-20 test queries. "Correct: 8/10, Partially correct: 1/10, Wrong: 1/10, Errors analyzed in `evaluation/analysis.md`."

Include a "What I Would Do With More Time" section in the README. List 3-5 improvements you would make: add reranking, implement caching, fine-tune embeddings, add a feedback loop, build a proper CI pipeline. This shows you see the full picture even if you did not have time to implement everything.

Make it easy to run. Docker is ideal but a clear `requirements.txt` with Python version and step-by-step instructions works. If the evaluator cannot run your code in under 5 minutes, they will not run it and will evaluate based only on code reading, which disadvantages you.

Do not over-engineer. If the assignment says "4 hours," a submission with Kubernetes configs, CI/CD pipelines, and 12 microservices signals that you either spent 40 hours or cannot scope work appropriately. Match the complexity to the time budget.


## PM Working Sessions (Paid Trials)

Some companies, especially later-stage startups, skip traditional interviews entirely and offer a paid working session (1-3 days, $500-2000). You work on a real problem from their codebase alongside the team.

What to expect:

Day 1: Onboarding. You get access to the codebase, documentation, and a specific problem to solve. Typically something like: "Our RAG system has low retrieval quality for questions about [specific topic]. Diagnose why and propose a fix."

Day 2: Execution. You implement your proposed solution, working in their environment with their tools. You may pair-program with a team member.

Day 3: Presentation. You present your findings, implementation, and results to the team. This includes a demo, metrics comparison (before vs. after), and a discussion of tradeoffs and next steps.

How to succeed:

Ask questions early. You are evaluated on how you approach an unfamiliar codebase, not on already knowing the answer. Ask about the data pipeline, evaluation metrics, known issues, and recent changes. This also shows collaboration skills.

Document as you go. Keep a running log of what you tried, what worked, what did not, and why. This becomes your presentation material and shows your debugging methodology.

Ship something small and working, then iterate. A targeted fix that improves retrieval quality by 5% on a specific failure mode is better than an ambitious redesign that is half-finished at the end of day 3.

Communicate proactively. If you are stuck, say so. If you find something unexpected in the data or code, mention it. If you realize your approach will not work, pivot and explain why. The team is evaluating you as a potential colleague, not as a coding machine. [^tryout-format-2024]


## Common Mistakes

Submitting without a working demo. If the evaluator cannot see your system answering questions, they cannot evaluate answer quality. A working demo with mediocre quality beats beautiful code that does not run.

Spending too much time on infrastructure and not enough on the core problem. If the assignment asks for a RAG system and you spend 4 of your 6 hours setting up Docker, CI, and a monitoring dashboard, your RAG pipeline will be a basic tutorial-level implementation. Prioritize the differentiating work.

Not reading the assignment carefully. If it says "include evaluation metrics," include evaluation metrics. If it says "explain your design decisions," write a design document. If it says "submit as a GitHub repo," do not send a zip file. Sounds obvious but I have seen many candidates lose points on these basics.

Using a framework without understanding it. If you use LangChain and the evaluator asks "what does `RetrievalQA` do under the hood?", you should be able to explain it. Using a framework is fine; being unable to explain what it does signals that you copy-pasted from a tutorial.

Ignoring edge cases. What happens when the user asks a question not covered by the corpus? When the input is in a different language? When the document is empty? When the API key is invalid? Handling 3-4 edge cases gracefully is enough to stand out.

Not including a `.env.example` file. If your code requires API keys, include a `.env.example` with placeholder values and instructions. An evaluator who has to read your code to figure out which environment variables to set will be frustrated before they even run your system.

Plagiarizing or using obvious template repos. Evaluators are aware of popular RAG tutorials and template repos. If your submission is a clone of a YouTube tutorial with variable names changed, it will be obvious. Use tutorials for learning but build your submission from scratch.


## Sources

[^gh-rag-take-home-2024]: Various GitHub repositories, "RAG Take-Home Assignment Templates," 2024 (aggregated from public repos shared by candidates)
[^gh-code-reviewer-assignment]: GitHub public repos, "AI Code Reviewer Take-Home," multiple companies 2024
[^gh-prd-processor]: GitHub public repos, "PRD to User Stories Generator," 2024
[^gh-text-to-sql]: GitHub public repos, "Natural Language to SQL Agent," 2024
[^gh-ticket-classifier]: GitHub public repos, "Support Ticket Classification Pipeline," 2024
[^gh-sentiment-pipeline]: GitHub public repos, "Review Sentiment Analysis Pipeline," 2024
[^gh-research-agent]: GitHub public repos, "AI Research Agent Take-Home," 2024
[^gh-finance-agent]: GitHub public repos, "Personal Finance AI Agent," 2024
[^gh-model-eval]: GitHub public repos, "Model Evaluation Take-Home," 2024
[^gh-chatbot-analysis]: GitHub public repos, "Chatbot Conversation Analysis," 2024
[^hiring-rubric-2024]: Compiled from publicly shared hiring rubrics and interview debrief formats, 2024
[^streamlit-docs]: Streamlit Documentation, https://docs.streamlit.io
[^gradio-docs]: Gradio Documentation, https://gradio.app/docs
[^tryout-format-2024]: Various engineering blog posts on paid trial interview formats, 2024
