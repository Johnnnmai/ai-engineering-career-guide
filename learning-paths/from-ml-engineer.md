# From ML Engineer to AI Engineer

This is the shortest transition path. You already build and deploy ML systems in production. The shift to AI engineering is less about learning new fundamentals and more about adapting to the LLM-centric stack that now dominates the field.

## What You Already Have

Your skill set is the closest match:

- Model training, evaluation, and deployment in production
- MLOps: pipelines, model registries, experiment tracking
- Feature engineering, data pipelines, ETL
- Model serving: latency optimization, batching, GPU management
- Monitoring model performance, detecting drift, retraining triggers
- Production engineering: APIs, containers, CI/CD
- Understanding of ML math: linear algebra, probability, optimization

## What You Need to Learn

The gap is LLM-specific patterns. Traditional ML engineering and LLM application engineering overlap significantly but diverge in important ways.

LLM application patterns (2-3 weeks):
- RAG architecture: chunking strategies (fixed, semantic, recursive), embedding model selection, vector database design, hybrid search (dense + sparse), reranking
- Agent patterns: ReAct, function calling, tool use, planning and decomposition, memory management
- Prompt engineering: system prompts, few-shot learning, chain-of-thought, structured output, prompt versioning and testing
- Guardrails: content filtering, output validation, hallucination detection

LLM-specific production challenges (2-3 weeks):
- Cost optimization: model routing (small model for simple queries, large for complex), caching, token budgeting
- Evaluation: LLM-as-judge, human evaluation pipelines, automated regression testing for non-deterministic outputs
- Fine-tuning in the LLM era: LoRA, QLoRA, dataset curation, instruction tuning, RLHF concepts
- Streaming responses, long-running inference, async processing
- Multi-model architectures: orchestrating multiple LLMs and traditional ML models

Emerging patterns (1-2 weeks):
- Multi-agent systems: task decomposition, agent communication, error recovery
- Retrieval-augmented generation at scale: document processing pipelines, incremental indexing
- Model distillation and deployment of smaller specialized models
- On-device inference and edge deployment for LLMs

## Suggested Timeline: 1-2 Months

Weeks 1-2: Build a production RAG system. Not a demo. A system with proper chunking, hybrid search, reranking, evaluation, and monitoring. Use your existing production skills to make it reliable.

Weeks 3-4: Build an agent system with tool use. Implement evaluation for agent behavior. Handle failure modes: infinite loops, tool errors, cost runaway.

Weeks 5-6: Interview preparation. Study LLM system design patterns (they differ from traditional ML system design). Practice articulating the differences between traditional ML pipelines and LLM application architectures.

Weeks 7-8 (if needed): Practice coding rounds and behavioral interviews. Your technical foundation is strong, so focus on communication and framing.

## Your Advantage

You are the candidate that hiring managers dream of finding. You understand both the model side and the infrastructure side. When an interviewer asks you to design an LLM-powered feature, you can talk about model selection, prompt design, evaluation, AND deployment, scaling, monitoring, and cost management.

The key differentiator: you know what production ML looks like. You have seen models fail in production. You understand that a model with 95% accuracy in evaluation can produce terrible results when real users interact with it. This experience is hard to teach and impossible to fake.

I found that ML engineers who transition to AI engineering roles often undersell themselves. They focus on what they do not know about LLMs instead of leading with their production experience. In interviews, lead with your production stories. Every interviewer has seen candidates who can explain transformer attention mechanisms. Fewer candidates can explain how they reduced model serving latency by 40% or designed a retraining pipeline that caught data drift before it affected users.

Your transition is about adding a new toolkit to an already strong foundation. You are not starting over. You are expanding.

For detailed interview preparation strategies and system design frameworks specific to AI engineering roles, see [The 0→1 AI Engineer Interview Playbook](https://valenx.org/books/aie).
