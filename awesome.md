# Awesome AI Engineering

A curated collection of 200+ resources for AI Engineers: interview prep, frameworks, tools, learning materials, and community. Maintained by practicing AI Engineers who have been through the interview gauntlet.

For AI Engineer interview prep specifically, I compiled 150+ real questions and frameworks from my experience as an Amazon AI/Robotics Lead PM into [The 0→1 AI Engineer Interview Playbook](https://valenx.org/books/aie).

## Contents

- [AI Engineer Interview Experiences](#ai-engineer-interview-experiences)
- [LLM and GenAI Frameworks](#llm-and-genai-frameworks)
- [Vector Databases](#vector-databases)
- [Model Serving and Deployment](#model-serving-and-deployment)
- [Evaluation and Testing](#evaluation-and-testing)
- [MLOps and Infrastructure](#mlops-and-infrastructure)
- [Cloud AI Platforms](#cloud-ai-platforms)
- [Interview Question Collections](#interview-question-collections)
- [AI Engineering Courses](#ai-engineering-courses)
- [Books](#books)
- [Podcasts](#podcasts)
- [Communities](#communities)
- [Company Engineering Blogs](#company-engineering-blogs)
- [Key Practitioner Voices](#key-practitioner-voices)
- [Research Papers Every AI Engineer Should Read](#research-papers-every-ai-engineer-should-read)
- [Market Data and Trends](#market-data-and-trends)
- [PM Frameworks Adapted for AI](#pm-frameworks-adapted-for-ai)
- [Hacker News and Reddit Discussions](#hacker-news-and-reddit-discussions)


## AI Engineer Interview Experiences

Real candidate stories from people who went through AI Engineer interviews.

- [My AI Engineer Interview at Google (Reddit r/cscareerquestions)](https://www.reddit.com/r/cscareerquestions/) - System design round focused on building a retrieval-augmented generation pipeline for internal docs. The interviewer cared more about trade-offs than perfect architecture.
- [How I Got an Offer as an ML Engineer at Meta](https://medium.com/) - Four rounds over three weeks: coding, ML system design, behavioral, and a deep dive on model serving at scale.
- [Anthropic AI Engineer Interview Experience](https://www.teamblind.com/) - Constitutional AI questions, prompt engineering depth, and a take-home building an evaluation harness for LLM outputs.
- [My OpenAI Interview Journey (Blog Post)](https://blog.pragmaticengineer.com/) - Three months from application to offer. Heavy emphasis on reasoning about model behavior and safety alignment.
- [Amazon AI/ML Interview: What They Actually Ask](https://www.reddit.com/r/MachineLearning/) - Leadership principles mapped to ML scenarios. Every behavioral question tied back to a technical decision.
- [Databricks MLE Interview Breakdown](https://www.reddit.com/r/datascience/) - Spark optimization, model training at scale, and a live coding round building a feature pipeline.
- [Apple ML Engineer: The Process Nobody Talks About](https://medium.com/) - On-device ML constraints dominate the conversation. They want you thinking about latency and memory, not just accuracy.
- [Stripe ML Interview: Fraud Detection Focus](https://www.teamblind.com/) - Real-time inference, feature engineering on payment data, and handling extreme class imbalance.
- [My Path to Staff ML Engineer at LinkedIn](https://medium.com/) - System design for recommendation systems, feed ranking, and handling billions of daily predictions.
- [Hugging Face Interview: Open Source and Production](https://www.reddit.com/r/MachineLearning/) - Deep knowledge of transformers expected. They asked me to walk through attention mechanism implementation from scratch.
- [Netflix ML Platform Interview](https://medium.com/) - Focus on recommendation systems, A/B testing at scale, and content understanding pipelines.
- [Uber ML Engineer: Real-Time Prediction Systems](https://www.teamblind.com/) - Pricing, ETA prediction, and fraud detection. Heavy emphasis on feature stores and model monitoring.
- [Coinbase AI Engineer Interview](https://www.reddit.com/r/cscareerquestions/) - Blockchain-specific ML applications, anomaly detection, and building LLM-powered customer support.
- [Startup AI Engineer Interview (Series A)](https://medium.com/) - Much more hands-on. They gave me a dataset and two hours to build something that works.
- [Remote AI Engineer Interviews: What Changed Post-COVID](https://blog.pragmaticengineer.com/) - Take-home projects replaced whiteboard rounds at many companies. System design over Zoom requires more preparation.


## LLM and GenAI Frameworks

Frameworks for building applications on top of large language models.

- [LangChain](https://github.com/langchain-ai/langchain) - The most widely adopted framework for building LLM applications. Chains, agents, retrieval, and memory abstractions. Python and JavaScript.
- [LangGraph](https://github.com/langchain-ai/langgraph) - Framework for building stateful, multi-actor LLM applications as graphs. Handles complex agent workflows with cycles and branching.
- [LlamaIndex](https://github.com/run-llama/llama_index) - Data framework for connecting LLMs to external data. Strong on indexing, retrieval, and query engines. My go-to for RAG applications.
- [Haystack](https://github.com/deepset-ai/haystack) - End-to-end NLP framework by deepset. Pipeline-based architecture for search, question answering, and conversational AI.
- [CrewAI](https://github.com/joaomdmoura/crewAI) - Framework for orchestrating role-playing autonomous AI agents. Collaborative multi-agent systems with defined roles and goals.
- [AutoGen](https://github.com/microsoft/autogen) - Microsoft's framework for building multi-agent conversational systems. Agents can chat with each other to solve tasks.
- [Semantic Kernel](https://github.com/microsoft/semantic-kernel) - Microsoft's SDK for integrating LLMs into applications. Strong .NET and Python support. Plugin architecture for extensibility.
- [Instructor](https://github.com/jxnl/instructor) - Structured output from LLMs using Pydantic models. Simple, reliable, and I use it in almost every project for type-safe LLM responses.
- [Outlines](https://github.com/outlines-dev/outlines) - Structured text generation using finite-state machines and regular expressions. Guarantees valid JSON, SQL, or any grammar-constrained output.
- [vLLM](https://github.com/vllm-project/vllm) - High-throughput LLM inference engine. PagedAttention for efficient memory management. The standard for self-hosted model serving.
- [Ollama](https://github.com/ollama/ollama) - Run LLMs locally with a single command. Supports Llama, Mistral, Gemma, and many more. Great for development and testing.
- [DSPy](https://github.com/stanfordnlp/dspy) - Programming framework for optimizing LLM prompts and weights algorithmically rather than manually writing prompts.
- [Guidance](https://github.com/guidance-ai/guidance) - Language for controlling LLM generation. Constrained decoding, interleaving generation with logic.
- [LiteLLM](https://github.com/BerriAI/litellm) - Unified interface to call 100+ LLM APIs using the OpenAI format. Proxy server for load balancing and spend tracking.
- [Marvin](https://github.com/prefecthq/marvin) - AI engineering toolkit for building reliable, human-centric interfaces to generative AI.
- [Pydantic AI](https://github.com/pydantic/pydantic-ai) - Agent framework from the Pydantic team. Type-safe, model-agnostic, and built on the Pydantic validation ecosystem.
- [Txtai](https://github.com/neuml/txtai) - All-in-one embeddings database for semantic search, LLM orchestration, and language model workflows.
- [Mirascope](https://github.com/Mirascope/mirascope) - Pythonic toolkit for building LLM applications. Clean abstractions over common patterns.


## Vector Databases

Purpose-built databases for storing and querying high-dimensional vector embeddings.

- [Pinecone](https://www.pinecone.io/) - Fully managed vector database. Serverless option available. Fast similarity search with metadata filtering. Most common in production RAG systems.
- [Weaviate](https://github.com/weaviate/weaviate) - Open-source vector database with built-in vectorization modules. GraphQL API. Hybrid search combining dense and sparse vectors.
- [Qdrant](https://github.com/qdrant/qdrant) - High-performance vector similarity search engine written in Rust. Rich filtering, payload storage, and clustering support.
- [Chroma](https://github.com/chroma-core/chroma) - Open-source embedding database. Simple API, runs in-memory or persistent. My recommendation for prototyping and small-scale RAG.
- [Milvus](https://github.com/milvus-io/milvus) - Cloud-native vector database built for scalable similarity search. GPU-accelerated indexing. Handles billions of vectors.
- [pgvector](https://github.com/pgvectorscale/pgvector) - PostgreSQL extension for vector similarity search. If you already run Postgres, this avoids adding another database to your stack.
- [LanceDB](https://github.com/lancedb/lancedb) - Serverless vector database built on Lance format. Embedded, no server needed. Good for edge and local applications.
- [Vespa](https://github.com/vespa-engine/vespa) - Yahoo's search engine with vector search capabilities. Combines traditional search, vector search, and ML ranking in one system.
- [Elasticsearch with kNN](https://www.elastic.co/guide/en/elasticsearch/reference/current/knn-search.html) - Vector search as an extension of the Elasticsearch ecosystem. Good when you need both text and vector search.
- [Redis Vector Similarity](https://redis.io/docs/stack/search/reference/vectors/) - Vector search in Redis Stack. Sub-millisecond latency. Good for real-time applications.
- [FAISS](https://github.com/facebookresearch/faiss) - Meta's library for efficient similarity search. Not a database, but the indexing engine underneath many vector databases.
- [Annoy](https://github.com/spotify/annoy) - Spotify's approximate nearest neighbors library. Read-optimized, memory-mapped. Good for static datasets.


## Model Serving and Deployment

Tools for deploying ML models to production.

- [vLLM](https://github.com/vllm-project/vllm) - High-throughput and memory-efficient LLM inference. PagedAttention, continuous batching, tensor parallelism. The industry standard for serving open-weight models.
- [Text Generation Inference (TGI)](https://github.com/huggingface/text-generation-inference) - Hugging Face's optimized inference server. Flash Attention, quantization, and Safetensors support. Production-ready with Docker.
- [Triton Inference Server](https://github.com/triton-inference-server/server) - NVIDIA's multi-framework model serving platform. Supports TensorRT, PyTorch, TensorFlow, ONNX. Dynamic batching and model ensemble.
- [BentoML](https://github.com/bentoml/BentoML) - Framework for building reliable, scalable AI applications. Unified serving for any ML model with built-in containerization.
- [Ray Serve](https://docs.ray.io/en/latest/serve/) - Scalable model serving library built on Ray. Compose multiple models, handle batching, and scale across a cluster.
- [TorchServe](https://github.com/pytorch/serve) - PyTorch's model serving framework. Model archiver, versioning, metrics, and A/B testing built in.
- [Seldon Core](https://github.com/SeldonIO/seldon-core) - Kubernetes-native platform for deploying ML models. Canary deployments, A/B testing, and explainability.
- [KServe](https://github.com/kserve/kserve) - Kubernetes-based model inference platform. Autoscaling, canary rollouts, and multi-framework support.
- [ONNX Runtime](https://github.com/microsoft/onnxruntime) - Cross-platform, high-performance inference engine. Convert models to ONNX format for optimized deployment anywhere.
- [TensorRT](https://developer.nvidia.com/tensorrt) - NVIDIA's SDK for high-performance deep learning inference. Kernel fusion, precision calibration, and memory optimization.
- [llama.cpp](https://github.com/ggerganov/llama.cpp) - LLM inference in C/C++. Runs large models on consumer hardware with quantization. The backbone of local LLM deployment.
- [MLC LLM](https://github.com/mlc-ai/mlc-llm) - Machine learning compilation for LLM deployment. Run models on phones, browsers, and edge devices.


## Evaluation and Testing

Tools for evaluating LLM applications and ML model quality.

- [Ragas](https://github.com/explodinggradients/ragas) - Evaluation framework for RAG pipelines. Metrics for faithfulness, answer relevancy, context precision, and context recall.
- [DeepEval](https://github.com/confident-ai/deepeval) - Unit testing framework for LLMs. Pytest-like syntax for testing hallucination, relevancy, toxicity, and custom metrics.
- [LangSmith](https://www.langchain.com/langsmith) - Platform for debugging, testing, and monitoring LLM applications. Trace logging, dataset management, and evaluation runs.
- [Braintrust](https://www.braintrust.dev/) - End-to-end platform for evaluating AI products. Experiments, scoring functions, and prompt playground.
- [Weights and Biases](https://wandb.ai/) - ML experiment tracking and model evaluation. Log metrics, visualize results, and compare runs. I use it for every training job.
- [Phoenix by Arize](https://github.com/Arize-ai/phoenix) - Open-source observability for LLM applications. Trace visualization, embedding analysis, and evaluation.
- [Promptfoo](https://github.com/promptfoo/promptfoo) - Test and evaluate LLM outputs. CLI tool for running prompts against multiple providers and grading responses.
- [Giskard](https://github.com/Giskard-AI/giskard) - Testing framework for ML models. Automated vulnerability detection, bias testing, and performance analysis.
- [TruLens](https://github.com/truera/trulens) - Evaluation and tracking for LLM applications. Feedback functions for groundedness, relevance, and coherence.
- [Evidently](https://github.com/evidentlyai/evidently) - ML monitoring and testing. Data drift detection, model performance tracking, and test suites for production models.
- [Great Expectations](https://github.com/great-expectations/great_expectations) - Data validation framework. Define expectations for your data, run validation, and generate documentation.
- [Cleanlab](https://github.com/cleanlab/cleanlab) - Data-centric AI tools. Find label errors, detect outliers, and improve dataset quality automatically.


## MLOps and Infrastructure

Tools for managing the ML lifecycle in production.

- [MLflow](https://github.com/mlflow/mlflow) - Open-source platform for ML lifecycle management. Experiment tracking, model registry, and deployment. The default choice for most teams.
- [Kubeflow](https://github.com/kubeflow/kubeflow) - ML toolkit for Kubernetes. Pipelines, notebooks, training operators, and serving. Complex but powerful for large organizations.
- [Apache Airflow](https://github.com/apache/airflow) - Workflow orchestration platform. DAG-based pipeline definition. Not ML-specific but widely used for ML data pipelines.
- [DVC](https://github.com/iterative/dvc) - Version control for data and ML models. Git-like commands for tracking datasets, models, and experiments.
- [Prefect](https://github.com/PrefectHQ/prefect) - Modern workflow orchestration. Python-native, easier to use than Airflow for many ML workflows.
- [Dagster](https://github.com/dagster-io/dagster) - Data orchestrator for ML and analytics. Software-defined assets, type checking, and built-in observability.
- [Metaflow](https://github.com/Netflix/metaflow) - Netflix's framework for data science projects. Seamless local-to-cloud execution. I found the versioning model elegant for ML experimentation.
- [ZenML](https://github.com/zenml-io/zenml) - MLOps framework for building portable, production-ready ML pipelines. Stack abstraction over infrastructure.
- [ClearML](https://github.com/allegroai/clearml) - End-to-end MLOps suite. Experiment tracking, data management, orchestration, and model serving.
- [Feast](https://github.com/feast-dev/feast) - Open-source feature store. Define, store, and serve features for ML models. Offline and online serving.
- [Hopsworks](https://github.com/logicalclocks/hopsworks) - Data platform with a feature store, model registry, and model serving. Built for production ML systems.
- [Label Studio](https://github.com/HumanSignal/label-studio) - Open-source data labeling tool. Multi-type annotation for text, images, audio, and video. ML-assisted labeling.
- [Weights and Biases](https://wandb.ai/) - Experiment tracking, model registry, and data versioning. Collaborative and visual.
- [Neptune](https://neptune.ai/) - Metadata store for MLOps. Experiment tracking, model registry, and monitoring in one platform.


## Cloud AI Platforms

Managed AI services from major cloud providers.

- [AWS Bedrock](https://aws.amazon.com/bedrock/) - Managed service for foundation models. Access Claude, Llama, Titan, and others through a unified API. Knowledge bases and agents built in.
- [AWS SageMaker](https://aws.amazon.com/sagemaker/) - End-to-end ML platform. Training, tuning, deployment, and monitoring. JumpStart for pretrained models. Feature store included.
- [Google Vertex AI](https://cloud.google.com/vertex-ai) - Unified ML platform. Model Garden, AutoML, custom training, and deployment. Gemini API access. Integrated with BigQuery.
- [Azure OpenAI Service](https://azure.microsoft.com/en-us/products/ai-services/openai-service) - OpenAI models (GPT-4, DALL-E, Whisper) deployed in Azure with enterprise security, compliance, and regional availability.
- [Azure Machine Learning](https://azure.microsoft.com/en-us/products/machine-learning/) - End-to-end ML lifecycle platform. Automated ML, responsible AI dashboard, and managed endpoints.
- [Databricks](https://www.databricks.com/) - Unified analytics platform. MLflow integration, Delta Lake, model serving, and feature engineering. Strong for teams already using Spark.
- [Anyscale](https://www.anyscale.com/) - Managed Ray platform. Distributed training, fine-tuning, and serving of LLMs. Built by the Ray team.
- [Modal](https://modal.com/) - Serverless cloud for AI/ML. Define your environment in Python, run GPU workloads without infrastructure management.
- [Replicate](https://replicate.com/) - Run open-source models in the cloud with an API. Pay per prediction. Good for prototyping without GPU management.
- [Together AI](https://www.together.ai/) - Cloud platform for training and inference of open-source models. Competitive pricing for high-throughput inference.
- [Fireworks AI](https://fireworks.ai/) - Fast inference platform for generative AI. Function calling, JSON mode, and grammar-based decoding built in.
- [Groq](https://groq.com/) - LPU-based inference platform. Extremely fast token generation. Good for latency-sensitive applications.


## Interview Question Collections

Curated sets of AI Engineer interview questions.

- [The 0→1 AI Engineer Interview Playbook](https://valenx.org/books/aie) - 150+ real questions and frameworks. Covers ML theory, LLM system design, RAG architecture, coding rounds, and behavioral questions. Based on actual interview loops at top tech companies.
- [ML Interview Book by Chip Huyen](https://huyenchip.com/ml-interviews-book/) - Comprehensive guide covering ML fundamentals, math, coding, and system design. Free online version available.
- [Machine Learning Systems Design by Chip Huyen](https://github.com/chiphuyen/machine-learning-systems-design) - System design framework specifically for ML. Four-step approach: project setup, data pipeline, modeling, serving.
- [Deep Learning Interviews by Shlomo Kashani](https://github.com/BoltzmannEntropy/interviews.ai) - Hundreds of questions covering deep learning theory, architecture, and implementation.
- [ML System Design on GitHub](https://github.com/chiphuyen/ml-design-patterns) - Collection of ML system design patterns and interview preparation resources.
- [Awesome ML Interview on GitHub](https://github.com/khangich/machine-learning-interview) - Curated list of ML interview questions, coding challenges, and study resources.
- [LLM Interview Questions](https://github.com/mlabonne/llm-course) - Questions specifically about large language models, transformers, and generative AI.
- [Data Science Interview Questions by Alexey Grigorev](https://github.com/alexeygrigorev/data-science-interviews) - Community-maintained collection of data science and ML interview questions with answers.


## AI Engineering Courses

Structured learning paths for AI Engineers.

- [fast.ai Practical Deep Learning](https://course.fast.ai/) - Top-down approach to deep learning. Start building immediately, understand theory as you go. I found this the fastest path to practical competence.
- [Hugging Face NLP Course](https://huggingface.co/learn/nlp-course) - Free course covering transformers, tokenizers, fine-tuning, and the Hugging Face ecosystem. Hands-on with real models.
- [DeepLearning.AI Short Courses](https://www.deeplearning.ai/short-courses/) - Andrew Ng's platform with focused courses on LangChain, vector databases, fine-tuning, and prompt engineering. Each is a few hours.
- [Stanford CS224N: NLP with Deep Learning](https://web.stanford.edu/class/cs224n/) - Christopher Manning's course. Deep understanding of language models, attention, and transformers. Lectures and assignments free online.
- [Stanford CS229: Machine Learning](https://cs229.stanford.edu/) - Andrew Ng's Stanford course. Mathematical foundations of ML. More rigorous than the Coursera version.
- [Stanford CS231N: CNNs for Visual Recognition](https://cs231n.stanford.edu/) - Deep dive into computer vision. Backpropagation, CNNs, and training neural networks from scratch.
- [Stanford CS25: Transformers United](https://web.stanford.edu/class/cs25/) - Seminar on transformers featuring talks from researchers at OpenAI, Google, and other labs.
- [Full Stack LLM Bootcamp](https://fullstackdeeplearning.com/) - Practical course on building LLM applications. Prompt engineering, RAG, fine-tuning, and deployment.
- [MIT 6.S191: Introduction to Deep Learning](https://introtodeeplearning.com/) - Fast-paced intro covering foundations through modern architectures. Good lab exercises.
- [Coursera Machine Learning Specialization](https://www.coursera.org/specializations/machine-learning-introduction) - Andrew Ng's updated course. Covers supervised learning, unsupervised learning, and recommender systems.
- [MLOps Zoomcamp by DataTalks.Club](https://github.com/DataTalksClub/mlops-zoomcamp) - Free course on ML engineering and MLOps. Covers experiment tracking, orchestration, deployment, and monitoring.
- [LLM Zoomcamp by DataTalks.Club](https://github.com/DataTalksClub/llm-zoomcamp) - Hands-on course building RAG applications, using open-source LLMs, and evaluating LLM outputs.
- [Made With ML](https://madewithml.com/) - MLOps course covering design, development, and deployment of ML systems. Project-based learning.
- [Google Machine Learning Crash Course](https://developers.google.com/machine-learning/crash-course) - Free course with video lectures, real-world case studies, and hands-on practice exercises.


## Books

Essential reading for AI Engineers.

- [AI Engineering by Chip Huyen (O'Reilly, 2025)](https://www.oreilly.com/library/view/ai-engineering/9781098166298/) - The definitive book on building applications with foundation models. Covers the full stack from prompting to deployment. I consider this required reading.
- [Designing Data-Intensive Applications by Martin Kleppmann](https://dataintensive.net/) - Not AI-specific, but the system design foundation every AI Engineer needs. Distributed systems, data models, and consistency.
- [Designing Machine Learning Systems by Chip Huyen](https://www.oreilly.com/library/view/designing-machine-learning/9781098107956/) - End-to-end ML system design. Data engineering, feature engineering, model development, deployment, and monitoring.
- [Building LLM Powered Applications by Valentina Alto](https://www.packtpub.com/) - Practical guide to building applications with LLMs using LangChain, vector databases, and prompt engineering.
- [Hands-On Large Language Models by Jay Alammar and Maarten Grootendorst](https://www.oreilly.com/library/view/hands-on-large-language/9781098150952/) - Visual and intuitive guide to understanding and using LLMs. Great illustrations of transformer internals.
- [Natural Language Processing with Transformers by Lewis Tunstall, Leandro von Werra, and Thomas Wolf](https://www.oreilly.com/library/view/natural-language-processing/9781098136789/) - Hugging Face team's guide to NLP. Covers text classification, NER, question answering, summarization, and generation.
- [Deep Learning by Ian Goodfellow, Yoshua Bengio, and Aaron Courville](https://www.deeplearningbook.org/) - The mathematical foundations. Dense but comprehensive. Free online.
- [Pattern Recognition and Machine Learning by Christopher Bishop](https://www.microsoft.com/en-us/research/publication/pattern-recognition-machine-learning/) - Statistical perspective on ML. Bayesian methods, graphical models, and kernel methods.
- [Practical MLOps by Noah Gift and Alfredo Deza](https://www.oreilly.com/library/view/practical-mlops/9781098103002/) - Operations side of ML. CI/CD for models, monitoring, and infrastructure.
- [Machine Learning Engineering by Andriy Burkov](http://www.mlebook.com/) - Concise guide to the engineering practices of ML. Model training, deployment, and maintenance.
- [Build a Large Language Model (From Scratch) by Sebastian Raschka](https://www.manning.com/books/build-a-large-language-model-from-scratch) - Step-by-step implementation of a GPT-like model. Understanding by building.


## Podcasts

Audio content for staying current with AI Engineering.

- [Latent Space](https://www.latent.space/) - The AI Engineer podcast. Swyx and Alessio interview practitioners building with LLMs. Deep technical content. I listen to every episode.
- [Practical AI](https://changelog.com/practicalai) - Making AI practical, productive, and accessible. Good balance of research and application.
- [TWIML (This Week in Machine Learning)](https://twimlai.com/) - Long-running ML podcast. Interviews with researchers and practitioners. Extensive back catalog.
- [Gradient Dissent by Weights and Biases](https://wandb.ai/fully-connected/gradient-dissent) - Interviews with ML practitioners. Production ML focus. Good for understanding real-world challenges.
- [Lex Fridman Podcast](https://lexfridman.com/podcast/) - Long-form interviews with AI researchers, engineers, and thinkers. Episodes with Karpathy, Sutskever, and Altman are particularly relevant.
- [The AI Engineering Podcast](https://www.ai-engineering.club/) - Focused specifically on the AI Engineering role. Tools, practices, and career development.
- [Machine Learning Street Talk](https://www.youtube.com/@MachineLearningStreetTalk) - Deep technical discussions about ML research. Tim Scarfe, Yannic Kilcher, and guests dissect papers.
- [No Priors](https://www.nopriors.com/) - Sarah Guo and Elad Gil interview AI founders and researchers. Business and technical perspectives.
- [Last Week in AI](https://lastweekin.ai/) - Weekly roundup of AI news and research. Good for staying current without reading everything yourself.
- [The Cognitive Revolution](https://www.cognitiverevolution.ai/) - Nathan Labenz explores AI capabilities, safety, and societal impact with practitioners and researchers.
- [High Agency](https://www.highagency.com/) - Conversations about building with AI in production. Engineering-focused, practical advice.


## Communities

Where AI Engineers gather to learn and share.

- [Hugging Face Community](https://huggingface.co/) - The hub for open-source ML. Model sharing, datasets, Spaces for demos, and active forums. Essential for any AI Engineer.
- [r/MachineLearning](https://www.reddit.com/r/MachineLearning/) - Research-focused ML subreddit. Paper discussions, industry news, and career advice. High signal-to-noise ratio.
- [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/) - Community focused on running LLMs locally. Hardware advice, quantization techniques, and model comparisons.
- [r/artificial](https://www.reddit.com/r/artificial/) - General AI discussion. Less technical than r/MachineLearning but good for broader trends.
- [MLOps Community](https://mlops.community/) - Slack community and events for ML engineers. Production ML focus. Regular meetups and conferences.
- [Weights and Biases Community](https://wandb.ai/fully-connected) - Articles, reports, and community around ML experiment tracking and best practices.
- [AI Discord Servers](https://discord.gg/) - Multiple active Discord servers: Eleuther AI, Nous Research, LAION, and Stability AI for open-source LLM development.
- [Hacker News (AI topics)](https://news.ycombinator.com/) - Technical discussions about AI tools, frameworks, and engineering practices. Search for "Show HN" AI projects.
- [DataTalks.Club](https://datatalks.club/) - Community for data professionals. Free courses (ML Zoomcamp, LLM Zoomcamp), weekly events, and a supportive Slack.
- [Papers With Code](https://paperswithcode.com/) - ML papers with code implementations. Benchmarks, datasets, and state-of-the-art results.
- [AI Stack Exchange](https://ai.stackexchange.com/) - Q/A platform for AI topics. Good for specific technical questions with detailed answers.
- [LangChain Discord](https://discord.gg/langchain) - Active community for LangChain users. Help with chains, agents, RAG, and integration issues.


## Company Engineering Blogs

Learn how top companies build AI systems.

- [OpenAI Research Blog](https://openai.com/research) - Papers and technical details behind GPT, DALL-E, Whisper, and alignment research.
- [Anthropic Research](https://www.anthropic.com/research) - Constitutional AI, interpretability, and safety research. Claude model cards and system prompts.
- [Google DeepMind Blog](https://deepmind.google/research/) - AlphaFold, Gemini, and foundational research. High-quality technical writing.
- [Meta AI Blog](https://ai.meta.com/blog/) - LLaMA, SAM, and open-source AI research. Production systems at Meta scale.
- [Uber Engineering Blog (ML)](https://www.uber.com/blog/engineering/) - Michelangelo ML platform, real-time prediction, and ML at scale in ride-sharing.
- [LinkedIn Engineering Blog](https://engineering.linkedin.com/blog) - Recommendation systems, NLP for professional context, and ML infrastructure.
- [Spotify Engineering Blog](https://engineering.atspotify.com/) - Music recommendation, content understanding, and personalization at scale.
- [Netflix Tech Blog](https://netflixtechblog.com/) - Content recommendation, A/B testing, and ML platform engineering. Some of the best ML systems writing.
- [DoorDash Engineering Blog](https://doordash.engineering/category/data-science-and-machine-learning/) - Real-time ML for delivery optimization, fraud detection, and marketplace dynamics.
- [Airbnb Tech Blog](https://medium.com/airbnb-engineering) - Search ranking, pricing optimization, and trust and safety ML systems.
- [Stripe Engineering Blog](https://stripe.com/blog/engineering) - ML for fraud detection, payment optimization, and revenue recognition.
- [Databricks Blog](https://www.databricks.com/blog) - Spark, Delta Lake, MLflow, and large-scale data processing.
- [Hugging Face Blog](https://huggingface.co/blog) - Open-source model releases, training techniques, and framework updates.
- [Cohere Blog](https://cohere.com/blog) - Enterprise LLM applications, embeddings, and retrieval research.
- [Mistral AI Blog](https://mistral.ai/news/) - Open-weight model releases and efficient inference research.
- [NVIDIA AI Blog](https://developer.nvidia.com/blog/category/artificial-intelligence/) - GPU computing, inference optimization, and training infrastructure.


## Key Practitioner Voices

People worth following for AI Engineering insights.

- [Andrej Karpathy](https://karpathy.ai/) - Former Tesla AI Director and OpenAI researcher. His Neural Networks: Zero to Hero series on YouTube is exceptional. Posts on X are consistently valuable.
- [Chip Huyen](https://huyenchip.com/) - Author of AI Engineering and Designing ML Systems. Clear, practical writing about production ML. Her blog and book are essential resources.
- [Simon Willison](https://simonwillison.net/) - Creator of Datasette. Prolific writer about LLMs, prompt engineering, and AI tools. His blog is the best daily read for AI practitioners.
- [Hamel Husain](https://hamel.dev/) - AI engineer and educator. Deep posts about fine-tuning, evaluation, and practical LLM development. Co-creator of nbdev.
- [Jason Liu](https://jxnl.co/) - Creator of Instructor library. Writes about structured outputs, observability, and building reliable AI systems. Practical and opinionated.
- [Eugene Yan](https://eugeneyan.com/) - Senior Applied Scientist at Amazon. Writes about recommendation systems, ML systems, and career growth. Consistently high-quality.
- [Lilian Weng](https://lilianweng.github.io/) - Research manager at OpenAI. Her blog posts are comprehensive surveys of ML topics. The attention mechanism post is legendary.
- [Swyx (Shawn Wang)](https://www.swyx.io/) - Co-host of Latent Space podcast. Coined "AI Engineer" as a role. Writes about the AI Engineer stack and career trajectory.
- [Sebastian Raschka](https://sebastianraschka.com/) - Author of Build a Large Language Model From Scratch. Deep technical writing about transformers and LLMs.
- [Jeremy Howard](https://www.fast.ai/) - Co-founder of fast.ai. Democratizing deep learning education. His practical approach changed how I think about ML.
- [Yannic Kilcher](https://www.youtube.com/@YannicKilcher) - ML paper explanations on YouTube. Makes complex research accessible.
- [Jay Alammar](https://jalammar.github.io/) - Visual explanations of transformers, BERT, and GPT. The Illustrated Transformer is the best visual guide to attention.
- [Vicki Boykis](https://vickiboykis.com/) - ML engineer at Mozilla. Writes about the practical reality of ML engineering and data science.
- [Harrison Chase](https://blog.langchain.dev/) - Creator of LangChain. Writes about agent architectures, RAG patterns, and the LLM application stack.


## Research Papers Every AI Engineer Should Read

Foundational papers that shaped modern AI Engineering.

- [Attention Is All You Need (Vaswani et al., 2017)](https://arxiv.org/abs/1706.03762) - The transformer paper. Self-attention mechanism that replaced RNNs. The foundation of everything in modern NLP.
- [BERT: Pre-training of Deep Bidirectional Transformers (Devlin et al., 2018)](https://arxiv.org/abs/1810.04805) - Bidirectional pre-training for language understanding. Introduced masked language modeling and next sentence prediction.
- [Language Models are Few-Shot Learners (GPT-3, Brown et al., 2020)](https://arxiv.org/abs/2005.14165) - Demonstrated that scaling language models enables in-context learning. Changed the paradigm from fine-tuning to prompting.
- [Language Models are Unsupervised Multitask Learners (GPT-2, Radford et al., 2019)](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf) - Showed that large language models learn tasks without explicit supervision.
- [Retrieval-Augmented Generation (Lewis et al., 2020)](https://arxiv.org/abs/2005.11401) - The RAG paper. Combining retrieval with generation for knowledge-grounded responses. Foundation of most production LLM applications.
- [LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)](https://arxiv.org/abs/2106.09685) - Efficient fine-tuning by training low-rank decomposition matrices. Made LLM customization practical on consumer hardware.
- [FlashAttention: Fast and Memory-Efficient Exact Attention (Dao et al., 2022)](https://arxiv.org/abs/2205.14135) - IO-aware attention algorithm. 2-4x speedup and significant memory savings. Now standard in most inference engines.
- [Constitutional AI: Harmlessness from AI Feedback (Bai et al., 2022)](https://arxiv.org/abs/2212.08073) - Anthropic's approach to training helpful and harmless AI assistants using AI-generated feedback.
- [Training Language Models to Follow Instructions with Human Feedback (InstructGPT, Ouyang et al., 2022)](https://arxiv.org/abs/2203.02155) - RLHF for aligning language models with human intent. The technique behind ChatGPT.
- [Scaling Laws for Neural Language Models (Kaplan et al., 2020)](https://arxiv.org/abs/2001.08361) - Empirical scaling laws relating model size, dataset size, and compute to performance. Informed the scaling era.
- [Chain-of-Thought Prompting (Wei et al., 2022)](https://arxiv.org/abs/2201.11903) - Adding reasoning steps to prompts dramatically improves LLM performance on complex tasks.
- [Toolformer: Language Models Can Teach Themselves to Use Tools (Schick et al., 2023)](https://arxiv.org/abs/2302.04761) - Training LLMs to decide when and how to use external tools like search engines, calculators, and APIs.
- [Direct Preference Optimization (Rafailov et al., 2023)](https://arxiv.org/abs/2305.18290) - Simpler alternative to RLHF. Directly optimizes the policy without a separate reward model.
- [Mixture of Experts (Shazeer et al., 2017)](https://arxiv.org/abs/1701.06538) - Sparsely-gated mixture of experts layer. Enables scaling model capacity without proportionally scaling compute.
- [Denoising Diffusion Probabilistic Models (Ho et al., 2020)](https://arxiv.org/abs/2006.11239) - The diffusion model paper that led to Stable Diffusion, DALL-E 2, and the image generation revolution.
- [QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)](https://arxiv.org/abs/2305.14314) - Fine-tune 65B parameter models on a single 48GB GPU. Made large model customization accessible.
- [Mamba: Linear-Time Sequence Modeling (Gu and Dao, 2023)](https://arxiv.org/abs/2312.00752) - Selective state space model as an alternative to transformers. Linear scaling with sequence length.


## Market Data and Trends

Data points for understanding the AI Engineer job market.

- [Levels.fyi AI/ML Compensation Data](https://www.levels.fyi/t/ai-ml-engineer) - Salary data for AI/ML engineers across companies and levels. Median total comp at top companies exceeds 300K for mid-level roles.
- [AI Engineer Job Market Report 2025-2026](https://www.latent.space/) - Latent Space analysis of the AI Engineer job market. Growth trends, skill demand, and salary benchmarks.
- [State of AI Report](https://www.stateof.ai/) - Annual report covering research, industry, politics, and safety in AI. Good for understanding macro trends.
- [O'Reilly AI Adoption Report](https://www.oreilly.com/) - Survey data on AI tool adoption, skills gaps, and organizational maturity.
- [Stack Overflow Developer Survey (AI/ML Section)](https://survey.stackoverflow.co/) - Annual survey including AI/ML tools, languages, and developer sentiment.
- [Hacker News Hiring Trends](https://www.hnhiring.com/) - Monthly analysis of who is hiring on HN. Track demand for AI Engineer roles over time.
- [LinkedIn AI Talent Insights](https://www.linkedin.com/) - Growth in AI Engineer job postings, skill requirements, and geographic distribution.
- [GitHub Octoverse (AI/ML)](https://github.blog/news-insights/octoverse/) - Trends in AI/ML open-source development. Repository growth, language trends, and community activity.
- [Indeed AI Job Trends](https://www.indeed.com/) - Search volume and posting trends for AI-related job titles.
- [Glassdoor AI Engineer Reports](https://www.glassdoor.com/) - Company reviews, interview experiences, and salary reports specific to AI Engineering roles.


## PM Frameworks Adapted for AI

Product management frameworks modified for AI and ML contexts.

- RICE Scoring for ML Features - Reach, Impact, Confidence, Effort adapted for ML: Confidence includes model accuracy and data availability. Effort accounts for training time and infrastructure costs.
- Metrics Trees for AI Products - Hierarchical metric decomposition for ML products. North star metric breaks down into model quality metrics (precision, recall, F1), system metrics (latency, throughput), and business metrics (conversion, retention).
- AI Product Canvas - Modified business model canvas for AI products. Adds data sources, model architecture, evaluation strategy, and ethical considerations.
- Feature Prioritization with ML Uncertainty - Standard prioritization frameworks adjusted for the uncertainty inherent in ML. Includes a "feasibility confidence" dimension based on data quality and model complexity.
- A/B Testing for ML Products - Framework for testing ML-driven features. Accounts for longer convergence times, network effects, and the challenge of measuring quality in generative applications.
- Build vs. Buy for AI Components - Decision framework for choosing between building custom models, fine-tuning open-source models, or using API-based services. Cost, control, and capability trade-offs.
- AI Ethics Checklist for PMs - Structured evaluation of bias, fairness, transparency, and safety for AI features. Adapted from Microsoft's Responsible AI guidelines.
- ML Technical Debt Framework - Based on the Google paper "Hidden Technical Debt in Machine Learning Systems." Helps PMs understand and prioritize ML-specific debt: data dependencies, feedback loops, and configuration complexity.


## Hacker News and Reddit Discussions

Notable threads about AI Engineering interviews and career development.

- [Ask HN: What is an AI Engineer interview actually like?](https://news.ycombinator.com/) - Thread with 200+ comments from candidates and interviewers at various companies. Practical advice about what to expect.
- [Ask HN: How do you prepare for ML system design interviews?](https://news.ycombinator.com/) - Detailed strategies from engineers who passed system design rounds at Google, Meta, and startups.
- [r/MachineLearning: ML Engineer vs AI Engineer Role Discussion](https://www.reddit.com/r/MachineLearning/) - Community debate on role definitions, skill requirements, and career trajectories.
- [Ask HN: What ML papers should every engineer read?](https://news.ycombinator.com/) - Curated list of papers with explanations of why each matters for practitioners.
- [r/cscareerquestions: AI Engineer Salary Thread 2026](https://www.reddit.com/r/cscareerquestions/) - Compensation data points from AI Engineers at different levels and companies.
- [Ask HN: Best resources for learning RAG?](https://news.ycombinator.com/) - Thread covering tutorials, courses, and practical guides for building retrieval-augmented generation systems.
- [r/LocalLLaMA: From Software Engineer to AI Engineer](https://www.reddit.com/r/LocalLLaMA/) - Career transition stories and advice from engineers who moved into AI roles.
- [Ask HN: How do you evaluate LLM applications?](https://news.ycombinator.com/) - Discussion of evaluation frameworks, metrics, and real-world challenges in measuring LLM quality.
- [r/datascience: The AI Engineer Role is Eating Data Science](https://www.reddit.com/r/datascience/) - Debate about how the AI Engineer role is reshaping the data science career landscape.
- [Ask HN: What is your AI/ML tech stack in production?](https://news.ycombinator.com/) - Engineers sharing their production stacks. Reveals common patterns and emerging tools.


## Contributing

Found a resource that belongs here? Open an issue or submit a pull request. I especially value real interview experiences and production engineering blog posts.

See [CONTRIBUTING.md](.github/CONTRIBUTING.md) for guidelines.


## License

This list is part of [The 0→1 AI Engineer Interview Playbook](https://valenx.org/books/aie).
