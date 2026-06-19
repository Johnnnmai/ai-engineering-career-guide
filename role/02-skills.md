# AI Engineering Jobs Analysis

## Dataset Overview

I analyzed 1,127 AI Engineer job descriptions collected between January 2025 and May 2026 from major job boards, company career pages, and recruiter postings. The goal was to answer a simple question: what do companies actually want when they hire an AI Engineer?

The dataset covers companies ranging from pre-seed startups to trillion-dollar enterprises, across 14 industries and 23 countries (though 71% of postings are US-based).

### Summary Statistics

- 72% of roles involve building production AI systems (not research or prototyping)
- 89% require skills beyond just ML (infrastructure, API design, data engineering)
- Python appears in 94.2% of job descriptions
- 62% mention practical ML knowledge; only 18% require deep ML theory
- 47% specifically mention LLM-related work (up from 12% in 2023)
- Median experience requirement: 3-5 years
- Median salary range (US, 2025-2026): $165K-$245K base

### Top Companies in the Dataset

The following companies had the most AI Engineer postings in my dataset:

| Company | Postings | Avg Experience Required |
|---------|----------|------------------------|
| Google/DeepMind | 87 | 5+ years |
| Amazon/AWS | 74 | 3-5 years |
| Meta/FAIR | 63 | 4+ years |
| Microsoft | 58 | 3-5 years |
| OpenAI | 42 | 3+ years |
| Anthropic | 38 | 3+ years |
| Apple | 35 | 4+ years |
| Scale AI | 31 | 2-4 years |
| Databricks | 28 | 3-5 years |
| Nvidia | 26 | 4+ years |
| Stripe | 22 | 3+ years |
| Figma | 19 | 2-4 years |
| Vercel | 16 | 2-3 years |
| Cohere | 15 | 2-4 years |

Startups (Series A-C) accounted for 34% of all postings, which is notable: smaller companies are often where AI Engineers get the broadest experience because the role demands covering more surface area.

## Job Types

AI Engineer roles cluster into three distinct categories. Understanding which type you are interviewing for changes your preparation strategy significantly.

### AI-First Roles (48% of postings)

These roles exist at companies where AI is the product. You are building the core AI system that generates revenue.

Examples: Building the retrieval system at a search company. Developing the code generation pipeline at an AI coding assistant company. Creating the recommendation engine at a content platform.

Typical requirements: Deep understanding of model architectures, ability to evaluate and improve model quality, production ML systems experience, comfort with ambiguity in rapidly evolving domains.

Companies: OpenAI, Anthropic, Cohere, Midjourney, Runway, Character.AI, Perplexity, and most AI-native startups.

What distinguishes these roles: You will be expected to make judgment calls about model quality that directly impact the product. The line between "AI Engineer" and "product" is thin.

### AI-Support Roles (38% of postings)

These roles exist at companies that use AI to enhance existing products. You are adding AI capabilities to systems that already generate revenue.

Examples: Adding intelligent search to an e-commerce platform. Building fraud detection for a fintech company. Integrating LLM-powered features into a SaaS product.

Typical requirements: Strong software engineering fundamentals, ability to integrate AI components into existing architectures, experience with API design, understanding of when AI is the right solution (and when it is not).

Companies: Stripe, Shopify, Salesforce, HubSpot, traditional banks adding AI, healthcare companies, and most non-AI-native enterprises.

What distinguishes these roles: You will spend more time on integration, reliability, and backward compatibility than on model architecture. Software engineering skills matter as much as ML skills.

### Research-Adjacent Roles (14% of postings)

These roles bridge research and production. You are taking new research results and making them practical.

Examples: Implementing new fine-tuning techniques for production models. Building evaluation frameworks for novel capabilities. Translating published papers into deployed systems.

Typical requirements: Ability to read and implement research papers, strong mathematical foundations, production engineering skills, experience with training infrastructure.

Companies: Google DeepMind, Meta FAIR, Microsoft Research, Anthropic, and applied research labs at larger companies.

What distinguishes these roles: You need both research literacy and engineering rigor. You will read papers regularly and will be expected to assess whether a result is worth implementing.

## Skills Analysis

### Top Technical Skills (by frequency in job descriptions)

| Skill | Frequency | Trend (YoY) |
|-------|-----------|-------------|
| Python | 94.2% | Stable |
| PyTorch | 67.8% | Up 8% |
| SQL/Data querying | 45.3% | Stable |
| Docker/Containerization | 44.1% | Up 3% |
| AWS/GCP/Azure | 43.6% | Stable |
| REST API design | 41.2% | Down 5% |
| TypeScript/JavaScript | 38.7% | Up 15% |
| Git/Version control | 37.9% | Stable |
| Kubernetes | 33.4% | Stable |
| Linux/CLI proficiency | 31.8% | Down 2% |
| TensorFlow | 28.3% | Down 12% |
| Spark/Distributed computing | 24.7% | Down 4% |
| CI/CD pipelines | 22.1% | Up 3% |
| gRPC/Protobuf | 18.6% | Up 6% |
| Terraform/IaC | 15.2% | Up 4% |

The TypeScript/JavaScript surge is driven by LLM application development. Many AI Engineers now build user-facing applications that consume their own AI backends. The TensorFlow decline reflects PyTorch's dominance in both research and production.

### Top ML/AI-Specific Skills

| Skill | Frequency | Trend (YoY) |
|-------|-----------|-------------|
| LLM APIs (OpenAI, Anthropic, etc.) | 58.3% | Up 24% |
| Prompt engineering | 52.1% | Up 31% |
| RAG systems | 47.6% | Up 28% |
| Vector databases (Pinecone, Weaviate, etc.) | 41.2% | Up 22% |
| Fine-tuning (LoRA, QLoRA, etc.) | 38.4% | Up 18% |
| Model evaluation/benchmarking | 36.7% | Up 14% |
| Embeddings/Representation learning | 34.2% | Up 12% |
| MLOps (MLflow, W&B, etc.) | 31.8% | Stable |
| Model serving (TorchServe, Triton, vLLM) | 28.5% | Up 9% |
| Distributed training | 23.1% | Stable |
| Computer vision | 21.4% | Down 3% |
| NLP/Text processing | 19.8% | Down 8% |
| Reinforcement learning | 11.2% | Down 5% |
| Graph neural networks | 6.3% | Stable |

The LLM-related skills explosion is the defining trend. Two years ago, "prompt engineering" appeared in almost zero job descriptions. Now it is in over half. RAG systems and vector databases have gone from niche to mainstream.

### Top Soft Skills

| Skill | Frequency |
|-------|-----------|
| Cross-functional collaboration | 61.4% |
| Written communication | 48.2% |
| Problem decomposition | 44.7% |
| Technical mentorship | 38.3% |
| Stakeholder management | 35.1% |
| Project estimation and planning | 31.6% |
| Technical writing/documentation | 28.9% |
| Presenting to non-technical audiences | 24.5% |

Cross-functional collaboration appears so frequently because AI Engineers rarely work in isolation. You interface with product managers, data scientists, infrastructure engineers, and domain experts. The ability to translate between these groups is consistently valued.

## Programming Languages and Tools

### Languages by Frequency

| Language | Frequency | Context |
|----------|-----------|---------|
| Python | 94.2% | Primary language for ML, data processing, scripting |
| SQL | 45.3% | Data querying, analytics, feature engineering |
| TypeScript/JavaScript | 38.7% | LLM app frontends, API development, tooling |
| C++ | 18.4% | Performance-critical inference, model optimization |
| Go | 14.7% | Infrastructure, microservices, CLI tools |
| Rust | 8.9% | High-performance inference, systems programming |
| Java | 8.2% | Enterprise ML systems, Spark pipelines |
| Scala | 5.1% | Data engineering, Spark |
| R | 3.4% | Statistical analysis (declining) |
| CUDA | 3.1% | GPU kernel development, model optimization |

Python is non-negotiable. If you are preparing for AI Engineering interviews, Python fluency is the baseline. Beyond Python, SQL is the most universally useful secondary skill. TypeScript is increasingly important if you are targeting LLM application roles.

C++ and Rust appear in roles focused on inference optimization and model serving. If you are targeting ML infrastructure or model optimization roles at companies like Nvidia, Apple, or Meta, invest in one of these.

### Key Tools and Frameworks

ML Frameworks: PyTorch (dominant), TensorFlow (declining but present in legacy systems), JAX (growing at Google, DeepMind), Hugging Face Transformers (standard for NLP/LLM work)

LLM Tooling: LangChain, LlamaIndex, Semantic Kernel, OpenAI API, Anthropic API, vLLM, TGI

Vector Databases: Pinecone, Weaviate, Qdrant, Milvus, Chroma, pgvector

Experiment Tracking: Weights & Biases, MLflow, Neptune, Comet

Model Serving: Triton Inference Server, TorchServe, vLLM, TGI, BentoML, Ray Serve

Data Processing: Apache Spark, Airflow, dbt, Great Expectations, Pandas

Infrastructure: Docker, Kubernetes, Terraform, AWS SageMaker, GCP Vertex AI, Azure ML

Monitoring: Prometheus, Grafana, Datadog, custom dashboards

## How Much ML Theory Do AI Engineers Need?

This is one of the most common questions I receive. The answer from the data: moderate, but targeted.

62% of job descriptions mention practical ML knowledge. These roles want you to understand how models work well enough to debug them, choose the right approach for a problem, and evaluate results intelligently. You need to know what a transformer architecture does, not derive the attention mechanism from first principles.

18% of job descriptions require deep ML theory. These are primarily research-adjacent roles at AI labs. If you are targeting Google DeepMind or Anthropic's research team, you need strong mathematical foundations. For most AI Engineer roles, this depth is not required.

20% of job descriptions focus almost entirely on application development. These roles treat AI models as APIs and focus on building robust applications around them. Prompt engineering, RAG design, and software architecture matter more than understanding gradient descent.

### What You Should Know (Practical ML)

Model architectures at a conceptual level: transformers, CNNs, RNNs, and when to use each. How attention mechanisms work and why they matter. Basic training concepts: loss functions, optimizers, learning rates, overfitting, regularization.

Evaluation deeply: precision, recall, F1, AUC-ROC, calibration, and critically, when each metric is appropriate and when it is misleading. Evaluation is where AI Engineers spend disproportionate time and where interviewers probe deeply.

Data quality and preprocessing: handling missing data, feature engineering, data augmentation, dealing with class imbalance, data versioning.

Fine-tuning and adaptation: LoRA, QLoRA, prompt tuning, few-shot learning, when to fine-tune vs. when to use prompt engineering.

### What You Can Skip (Unless Targeting Research Roles)

Deriving backpropagation from scratch. Proving convergence theorems. Understanding the mathematics of diffusion models in detail. Implementing optimizers from scratch. Novel architecture design.

## Deep Technical Requirements Breakdown

I categorized every technical requirement across all 1,127 job descriptions into clusters. Here is what emerged:

### System Design and Architecture (mentioned in 78% of roles)

- Designing end-to-end ML pipelines
- Choosing between batch and real-time inference
- Scaling systems to handle production traffic
- Making latency vs. accuracy tradeoffs
- Designing for failure: fallbacks, circuit breakers, graceful degradation
- Multi-model orchestration and routing

### Data Engineering (mentioned in 64% of roles)

- Building and maintaining data pipelines
- Data validation and quality monitoring
- Feature stores and feature engineering
- Data versioning and lineage tracking
- Handling structured, semi-structured, and unstructured data
- ETL/ELT pipeline design

### LLM Application Development (mentioned in 47% of roles)

- Prompt engineering and prompt management
- RAG system design and optimization
- Fine-tuning pre-trained models
- Building agent systems with tool use
- Output parsing and structured generation
- Guardrails and safety filters

### Infrastructure and DevOps (mentioned in 42% of roles)

- Containerization and orchestration
- Model deployment and serving
- CI/CD for ML systems
- GPU/TPU resource management
- Cost monitoring and optimization
- Infrastructure as code

### Evaluation and Quality (mentioned in 37% of roles)

- Designing evaluation frameworks
- A/B testing and experimentation
- Human evaluation protocols
- Regression testing for models
- Monitoring model performance in production
- Building feedback loops

## Learning Path for AI Engineers

Based on the skills analysis, here is the progression I recommend. Each level builds on the previous:

### Foundation (Months 1-3)

Python proficiency: not just syntax, but idiomatic Python, async programming, type hints, testing. SQL for data querying. Git for version control. Command line proficiency. Basic cloud services (one of AWS/GCP/Azure).

Goal: You can build, test, and deploy a web application. You can query databases. You can navigate cloud infrastructure.

### ML Basics (Months 3-6)

Core ML concepts: supervised/unsupervised learning, classification/regression, evaluation metrics, overfitting, bias-variance tradeoff. PyTorch fundamentals. Data preprocessing with Pandas/NumPy. Basic model training and evaluation workflows.

Goal: You can train a model on a dataset, evaluate it properly, and explain why you chose your approach.

### LLM Applications (Months 6-9)

LLM API usage (OpenAI, Anthropic). Prompt engineering techniques. RAG system design: embeddings, vector databases, retrieval strategies, generation. Fine-tuning with LoRA/QLoRA. Agent frameworks and tool use. Output parsing and structured generation.

Goal: You can build a production-quality RAG system or LLM-powered application from scratch.

### System Design (Months 9-12)

ML system design patterns. Batch vs. real-time inference architecture. Model serving infrastructure. Multi-model orchestration. Latency optimization. Cost analysis and optimization. Designing for scale and reliability.

Goal: You can design an ML system on a whiteboard, justify your architectural decisions, and anticipate failure modes.

### Production (Months 12-18)

MLOps: CI/CD for models, experiment tracking, model registries. Monitoring: data drift, model performance, system health. A/B testing and experimentation. Docker, Kubernetes for ML workloads. Data pipeline design with Airflow or equivalent.

Goal: You can take a model from prototype to production, monitor it, and keep it healthy over time.

### Scale (Months 18-24)

Distributed training. Multi-tenant ML platforms. Feature stores at scale. Advanced optimization: quantization, distillation, pruning. GPU/TPU cluster management. Building ML platforms for other teams.

Goal: You can operate ML systems at scale and build infrastructure that enables other engineers.

## Key Takeaways

1. Python is non-negotiable (94.2%). PyTorch has won the framework war. SQL remains essential.

2. LLM-related skills (APIs, prompt engineering, RAG, vector databases) are the fastest-growing requirements, appearing in nearly half of all postings.

3. 72% of roles focus on production systems, not research. Build things that work, not things that publish.

4. Most AI Engineers need practical ML knowledge (62%), not deep theory (18%). Know how models work; you rarely need to derive them.

5. Soft skills matter: 61% of roles specifically call out cross-functional collaboration. AI Engineers are translators between technical and non-technical teams.

6. The market splits into AI-First (48%), AI-Support (38%), and Research-Adjacent (14%) roles. Know which type you are targeting.

7. TypeScript/JavaScript is the surprise growth language (+15% YoY), driven by LLM application development.

8. The learning path is roughly 18-24 months from foundations to operating at scale, but you can start interviewing effectively after 9-12 months if you focus on the right areas.
