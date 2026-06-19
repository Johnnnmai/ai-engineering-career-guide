# ML Fundamentals and AI Theory Questions

Format: 45-60 min conversational interview. The interviewer probes depth by asking follow-ups, so surface-level answers get exposed fast. I found that the best preparation is teaching each concept to someone else aloud until you can explain it without notes.


## Neural Network Architecture

Q: Walk me through the transformer architecture from input to output.

Start with the input embedding layer, where tokens get mapped to dense vectors of dimension d_model (typically 512 or 768). Add positional encodings (sinusoidal in the original paper, learned in most modern implementations). The encoder stack has N identical layers, each containing multi-head self-attention followed by a position-wise feed-forward network, with residual connections and layer normalization around each sub-layer. The decoder stack mirrors this but adds a cross-attention layer that attends to encoder outputs, and the self-attention is masked to prevent attending to future positions. The final output passes through a linear layer and softmax to produce next-token probabilities. [^vaswani-2017]

Follow-up: Why did transformers replace RNNs and LSTMs?

Three reasons. First, parallelization: RNNs process tokens sequentially, so training time scales linearly with sequence length. Transformers process all positions simultaneously. Second, long-range dependencies: even LSTMs struggle with sequences beyond a few hundred tokens because gradients still degrade over long chains. Attention computes direct connections between any two positions regardless of distance. Third, scaling: transformers benefit predictably from more compute and data, which RNNs do not. The tradeoff is that attention has O(n^2) complexity in sequence length, which is why context window extension remains an active research area. [^vaswani-2017] [^kaplan-scaling-2020]

Q: Explain multi-head attention. Why multiple heads instead of one big attention?

Each head learns to attend to different aspects of the input. One head might capture syntactic relationships (subject-verb), another might capture semantic similarity, another might capture positional patterns. Mechanically, you project Q, K, V through separate learned linear transformations for each head, compute scaled dot-product attention independently, concatenate the results, and project through one more linear layer. Multiple smaller heads are also more parameter-efficient than one large attention computation with the same total dimensionality. [^vaswani-2017]

Q: What is the difference between BERT and GPT architecturally?

BERT uses only the encoder stack with bidirectional self-attention (every token attends to every other token). It is pretrained with masked language modeling (predict the masked token) and next-sentence prediction. GPT uses only the decoder stack with causal (left-to-right) self-attention, pretrained with autoregressive language modeling (predict the next token). This architectural choice determines their strengths: BERT excels at understanding tasks (classification, NER, question answering over a given passage) while GPT excels at generation tasks (text completion, summarization, dialogue). Modern LLMs like GPT-4, Claude, and Llama are all decoder-only. [^devlin-bert-2019] [^radford-gpt2-2019]

Q: Explain residual connections and why they matter in deep networks.

A residual connection adds the input of a sub-layer directly to its output: `output = sublayer(x) + x`. Without residual connections, gradients must flow through every layer during backpropagation, and in deep networks (50+ layers) they vanish or explode. Residual connections provide a gradient highway that allows gradients to flow directly to earlier layers. They also make it easier for the network to learn identity mappings, meaning adding more layers never hurts performance (the extra layers can simply learn to do nothing). He et al. demonstrated this with ResNets going to 152 layers in computer vision. In transformers, residual connections wrap both the attention and feed-forward sub-layers. [^he-resnet-2015]

Q: What is layer normalization and how does it differ from batch normalization?

Batch normalization normalizes across the batch dimension (computing mean and variance across all examples in a mini-batch for each feature). Layer normalization normalizes across the feature dimension (computing mean and variance across all features for each individual example). Layer norm is preferred in transformers because it does not depend on batch size, works identically during training and inference, and handles variable-length sequences naturally. Batch norm requires tracking running statistics and behaves differently at train vs. inference time. [^ba-layernorm-2016]


## Training and Optimization

Q: Explain backpropagation in plain terms.

Backpropagation is the chain rule applied systematically through a computational graph. During the forward pass, you compute the loss. During the backward pass, you compute the gradient of the loss with respect to every parameter by walking backward through the graph, multiplying local gradients at each node. These gradients tell each parameter how to adjust to reduce the loss. The key insight is that you can compute gradients for millions of parameters in roughly the same time as one forward pass by reusing intermediate computations. [^rumelhart-backprop-1986]

Q: Explain the bias-variance tradeoff.

Bias measures how far your model's average prediction is from the true value (systematic error). Variance measures how much your predictions change across different training sets (sensitivity to training data). High bias means underfitting (model too simple to capture the pattern). High variance means overfitting (model memorizes training noise). Total error decomposes as: error = bias^2 + variance + irreducible noise. In practice, you manage this tradeoff through model complexity, regularization, and training data size. Deep learning somewhat breaks the classical picture because very large models can have low bias and low variance simultaneously when properly regularized, a phenomenon called "double descent." [^hastie-esl-2009] [^nakkiran-double-descent-2019]

Q: What regularization techniques do you know, and when would you use each?

L1 regularization (lasso) adds the sum of absolute weights to the loss. It encourages sparsity, making some weights exactly zero. Good for feature selection. L2 regularization (ridge, weight decay) adds the sum of squared weights. It encourages small weights but not zero weights. Standard choice for most neural networks. Dropout randomly zeros out neurons during training, forcing the network to learn redundant representations. Effective in fully connected layers, less common in modern transformer architectures which favor other approaches. Early stopping halts training when validation loss stops improving. Data augmentation effectively regularizes by increasing the diversity of training data. In LLM fine-tuning, LoRA implicitly regularizes by constraining updates to a low-rank subspace. [^srivastava-dropout-2014] [^loshchilov-decoupled-2019]

Q: Compare Adam, SGD with momentum, and AdamW. When would you pick each?

SGD with momentum is the simplest: it adds a fraction of the previous update to the current gradient, smoothing out oscillations. It often generalizes better than Adam but requires more careful learning rate tuning. Adam combines momentum with per-parameter adaptive learning rates using first and second moment estimates. It converges faster and is less sensitive to learning rate choice, making it the default for most deep learning. AdamW fixes a subtle bug in Adam's weight decay implementation: original Adam applies L2 regularization to the gradient before the adaptive step, which couples regularization with the learning rate. AdamW decouples them, applying weight decay directly to the weights. AdamW is the standard optimizer for transformer training. [^kingma-adam-2015] [^loshchilov-decoupled-2019]

Q: What are learning rate schedules and why do they matter?

The learning rate controls step size during gradient descent. Too high and training diverges. Too low and it gets stuck or takes forever. Schedules adjust the learning rate during training. Common ones: warmup (start low, ramp up linearly over the first few thousand steps to stabilize training), cosine annealing (gradually decrease following a cosine curve), step decay (drop by a factor at specific epochs). The warmup + cosine decay schedule is standard for transformer pretraining. More recently, the WSD (warmup-stable-decay) schedule has shown strong results for LLM training, where the learning rate is held constant for most of training and decayed only at the end. [^loshchilov-cosine-2017] [^hu-minicpm-2024]


## LLM Fundamentals

Q: Explain tokenization. Why not just use characters or words?

Character-level tokenization creates very long sequences (expensive for attention) and makes it hard to learn word-level semantics. Word-level tokenization cannot handle out-of-vocabulary words and creates enormous vocabularies. Subword tokenization (BPE, WordPiece, SentencePiece) is the compromise. BPE starts with individual characters and iteratively merges the most frequent adjacent pairs until reaching a target vocabulary size (typically 32K-100K tokens). This handles rare words by breaking them into known subwords while keeping common words as single tokens. Tokenization has real consequences: GPT-style tokenizers are biased toward English (non-Latin scripts use more tokens per word, increasing cost and reducing effective context length). [^sennrich-bpe-2016] [^kudo-sentencepiece-2018]

Q: What are embeddings and why are they useful?

Embeddings are dense vector representations of discrete items (words, tokens, users, products) in a continuous vector space. Similar items end up close together in this space. They are useful because neural networks operate on continuous numbers, not discrete symbols, and because they capture semantic relationships (the classic example: king - man + woman ~ queen). In LLMs, each token gets a learned embedding vector. For retrieval systems, embeddings from models like `text-embedding-3-large` or `e5-large-v2` encode semantic meaning so that similar documents have high cosine similarity. The dimensionality (256 to 4096) trades off between expressiveness and computational cost. [^mikolov-word2vec-2013]

Q: Explain temperature and top-p sampling in language model generation.

During generation, the model outputs a probability distribution over the vocabulary. Temperature scales the logits before softmax: temperature < 1 sharpens the distribution (more deterministic, favoring high-probability tokens), temperature > 1 flattens it (more random, spreading probability to unlikely tokens), temperature = 0 is greedy decoding (always pick the most probable token). Top-p (nucleus) sampling truncates the distribution to the smallest set of tokens whose cumulative probability exceeds p, then samples from that set. Top-p = 0.9 means you sample from the tokens that together account for 90% of the probability mass. In practice, you typically use temperature or top-p, not both aggressively. For factual tasks, low temperature. For creative tasks, higher temperature with moderate top-p. [^holtzman-nucleus-2020]

Q: What is a context window and why is extending it hard?

The context window is the maximum number of tokens the model can process in one forward pass. Original transformers had windows of 512-2048 tokens. Modern LLMs range from 8K (early GPT-4) to 200K (Claude) to 1M+ (Gemini). Extending the context window is hard because attention is O(n^2) in sequence length (both compute and memory), and because positional encodings trained on short sequences do not generalize to longer ones. Solutions include RoPE (rotary position embeddings) with NTK-aware scaling, ALiBi (linear attention bias), sliding window attention, and various efficient attention mechanisms (Flash Attention reduces memory but not compute complexity). A longer context window does not guarantee the model uses all of it effectively. Studies show performance degrades for information in the middle of long contexts (the "lost in the middle" problem). [^press-alibi-2022] [^liu-lost-middle-2023] [^dao-flashattn-2022]


## Classical ML

Q: When would you use a random forest over a neural network?

Random forests are preferable when you have tabular data with fewer than 100K rows, need interpretable results, have limited compute, want fast training with minimal hyperparameter tuning, or need to handle missing values gracefully. Neural networks are better for unstructured data (text, images, audio), very large datasets, or when you need to learn complex representations. In practice, gradient-boosted trees (XGBoost, LightGBM) dominate tabular data benchmarks and are still the go-to for most production tabular ML. Neural networks have not convincingly beaten tree-based methods on tabular data despite many attempts. [^grinsztajn-trees-tabular-2022]

Q: Explain k-means clustering and its limitations.

K-means partitions data into k clusters by iteratively assigning each point to the nearest centroid and recomputing centroids as cluster means. Limitations: you must specify k in advance, it assumes spherical clusters of similar size, it is sensitive to initialization (mitigated by k-means++ initialization), it converges to local optima, and it uses Euclidean distance which is problematic in high dimensions. Alternatives include DBSCAN (density-based, finds arbitrary shapes, does not require k), hierarchical clustering (produces a dendrogram), and Gaussian Mixture Models (probabilistic, allows soft assignments and elliptical clusters). [^lloyd-kmeans-1982]

Q: What is the curse of dimensionality?

As the number of features increases, the volume of the feature space grows exponentially, making data sparse. Distances between points become more uniform (everything is far from everything else), which breaks distance-based methods like KNN and K-means. You need exponentially more data to maintain the same density. In practice, this means dimensionality reduction (PCA, t-SNE, UMAP) is often a necessary preprocessing step for classical ML, and why feature engineering to reduce dimensionality matters. Deep learning partially sidesteps this by learning compressed representations through its layers. [^bellman-1961]


## Evaluation Metrics

Q: Explain precision, recall, and F1. When do you optimize for each?

Precision = true positives / (true positives + false positives). Of everything you flagged positive, how many were actually positive. Recall = true positives / (true positives + false negatives). Of everything that was actually positive, how many did you catch. F1 = harmonic mean of precision and recall. Optimize for precision when false positives are expensive (spam filter marking important emails as spam, content moderation blocking legitimate content). Optimize for recall when false negatives are dangerous (cancer screening, fraud detection, safety-critical systems). F1 when you need a balanced single metric. In LLM applications, precision maps to "hallucination rate" (how often does the model assert something false) and recall maps to "completeness" (does the model find all relevant information). [^manning-ir-2008]

Q: What is perplexity and how do you interpret it?

Perplexity measures how surprised the model is by the test data. Mathematically, it is 2 raised to the power of the cross-entropy loss, or equivalently, the exponential of the average negative log-likelihood per token. A perplexity of 10 means the model is as uncertain as if it were choosing uniformly among 10 options at each step. Lower is better. GPT-2 had perplexity around 35 on WikiText-103; modern LLMs achieve single digits. Perplexity is useful for comparing models on the same dataset but does not directly measure task performance (a model with lower perplexity can still be worse at following instructions). [^jurafsky-slp-2023]

Q: How do you evaluate generation quality in NLP?

BLEU measures n-gram overlap between generated and reference text. It correlates with quality for machine translation but poorly for open-ended generation. ROUGE measures recall of n-grams from reference in generated text. ROUGE-L uses longest common subsequence. Both are cheap and fast but do not capture semantic similarity. BERTScore computes token-level similarity using contextual embeddings, better correlating with human judgment. For LLM evaluations, the field has moved toward LLM-as-judge (using a strong model like GPT-4 or Claude to rate outputs), human evaluation with detailed rubrics, and task-specific benchmarks (MMLU, HumanEval, GSM8K). Each has limitations: LLM judges have biases (verbosity bias, position bias), human eval is expensive and inconsistent. [^papineni-bleu-2002] [^lin-rouge-2004] [^zheng-lmsys-2023]


## Statistics and Probability

Q: Explain Bayes theorem and give a practical application.

Bayes theorem: P(A|B) = P(B|A) * P(A) / P(B). It updates the probability of a hypothesis given new evidence. The classic practical example is spam filtering. P(spam|word) = P(word|spam) * P(spam) / P(word). If the word "lottery" appears in 80% of spam emails but only 1% of legitimate ones, seeing "lottery" dramatically increases the probability of spam. In ML, Bayesian thinking underpins naive Bayes classifiers, Bayesian hyperparameter optimization, and the conceptual framework for updating beliefs about model performance as you gather more evaluation data. [^murphy-mlpp-2012]

Q: When is A/B testing insufficient for evaluating ML systems?

A/B testing works when you have enough traffic, the metric is measurable in a short window, and the treatment effect is large enough to detect. It breaks down for ML systems in several ways. First, delayed effects: a recommendation model might increase short-term clicks but decrease long-term retention (needs long holdout experiments). Second, interference: in social networks, treated and control users interact, contaminating the control group. Third, rare events: if your model prevents fraud, you need enormous sample sizes to detect differences in already-rare fraud rates. Fourth, distributional shift: the model changes user behavior, which changes the data distribution, which changes model performance (feedback loops). Fifth, multiple metrics: ML systems affect many metrics simultaneously and you need to reason about tradeoffs, not just statistical significance on one number. Interleaving experiments and bandit approaches are alternatives. [^kohavi-trustworthy-2020]


## Information Retrieval

Q: Explain BM25 and how it improves over TF-IDF.

TF-IDF scores a document for a query term based on term frequency (how often the term appears in the document) and inverse document frequency (how rare the term is across all documents). BM25 improves on this in three ways. First, it adds term frequency saturation: after a term appears several times, additional occurrences contribute less (controlled by parameter k1). TF-IDF grows linearly forever. Second, it normalizes for document length (controlled by parameter b), so longer documents are not unfairly advantaged. Third, it handles multi-term queries more robustly. BM25 remains a strong baseline and is the default algorithm in Elasticsearch and similar systems. It is competitive with or superior to neural retrievers on some benchmarks, especially with limited training data. [^robertson-bm25-2009]

Q: What is semantic search and how does it differ from keyword search?

Keyword search (BM25, TF-IDF) matches based on exact terms. If the query says "car" and the document says "automobile," keyword search misses it. Semantic search uses dense vector embeddings to represent both queries and documents in a continuous vector space where semantically similar content has high cosine similarity. You embed the query, search a vector index (FAISS, HNSW) for the nearest document embeddings, and return results. Strengths: handles synonyms, paraphrases, and conceptual similarity. Weaknesses: can miss exact keyword matches that BM25 catches, does not handle negation well ("restaurants without sushi" might still return sushi restaurants), and embedding quality depends on the training data. In practice, hybrid search (combining BM25 and semantic scores) outperforms either alone. [^karpukhin-dpr-2020]


## Specialized Topics

Q: Explain reinforcement learning at a high level. Where is it used in AI today?

RL trains an agent to take actions in an environment to maximize cumulative reward. The agent observes a state, takes an action, receives a reward and a new state, and updates its policy. Key concepts: policy (mapping from states to actions), value function (expected future reward from a state), exploration vs. exploitation (trying new actions vs. sticking with known good ones). In modern AI, RL is used primarily in two places. First, RLHF (reinforcement learning from human feedback) for aligning LLMs: train a reward model on human preference data, then use PPO or DPO to fine-tune the LLM to maximize the reward model's score. Second, game-playing and robotics (AlphaGo, robotic manipulation). RLHF is increasingly being replaced by DPO (direct preference optimization) which skips the reward model step. [^sutton-rl-2018] [^ouyang-instructgpt-2022] [^rafailov-dpo-2023]

Q: Explain attention in the context of computer vision.

Vision Transformers (ViT) split an image into fixed-size patches (typically 16x16 pixels), flatten each patch into a vector, add positional embeddings, and process them through standard transformer encoder layers. Each patch attends to every other patch, allowing the model to capture global relationships that CNNs (limited by kernel size) miss without very deep stacking. The tradeoff is that ViTs need more training data than CNNs to match performance (CNNs have useful inductive biases like translation invariance). Hybrid architectures and pretrained ViTs (CLIP, DINOv2) now dominate vision benchmarks. [^dosovitskiy-vit-2021]


## How to Prepare

I recommend a three-phase approach over 2-4 weeks.

Phase 1 (week 1): Reread the transformer paper (Vaswani et al. 2017) and implement multi-head attention from scratch in PyTorch or NumPy. This single exercise covers 60% of theory questions. Also review the bias-variance tradeoff, gradient descent, and regularization from any ML textbook.

Phase 2 (week 2): Study LLM-specific topics: tokenization, sampling strategies, RLHF/DPO, scaling laws. Read the InstructGPT paper for RLHF and the LLaMA paper for modern pretraining practices.

Phase 3 (weeks 3-4): Practice explaining concepts aloud. Record yourself answering questions for 2 minutes each. If you cannot explain it clearly without notes, you do not understand it well enough.

Resources that I found most useful:
- Andrej Karpathy's "Neural Networks: Zero to Hero" YouTube series
- Jay Alammar's illustrated transformer blog post
- Chip Huyen's "Designing Machine Learning Systems" (O'Reilly, 2022)
- Stanford CS224N lecture recordings (free on YouTube)
- The Hugging Face NLP course (free)


## Common Mistakes

Giving textbook definitions without intuition. Interviewers want to know you understand the concept, not that you memorized a formula. When asked about attention, draw it out, explain what each matrix does, and give an example of what a head might learn.

Not knowing the tradeoffs. Every technique has limitations. If you explain BM25 without mentioning it cannot handle synonyms, or explain transformers without mentioning O(n^2) complexity, the interviewer will think you only know the happy path.

Confusing architecture details. Mixing up encoder-only (BERT), decoder-only (GPT), and encoder-decoder (T5, original transformer) is surprisingly common and signals shallow understanding.

Over-indexing on classical ML. If you spend 10 minutes explaining SVMs in detail but cannot explain how attention works, your preparation is misallocated for an AI Engineer role in 2025-2026. Know classical ML at a conceptual level but go deep on transformers and LLMs.

Ignoring practical implications. When discussing context windows, mention the cost and latency implications. When discussing fine-tuning, mention when to use LoRA vs. full fine-tuning and the compute requirements. Interviewers want to see that you think about engineering constraints, not just theory.


## Sources

[^vaswani-2017]: Vaswani et al., "Attention Is All You Need," NeurIPS 2017
[^kaplan-scaling-2020]: Kaplan et al., "Scaling Laws for Neural Language Models," OpenAI 2020
[^devlin-bert-2019]: Devlin et al., "BERT: Pre-training of Deep Bidirectional Transformers," NAACL 2019
[^radford-gpt2-2019]: Radford et al., "Language Models are Unsupervised Multitask Learners," OpenAI 2019
[^he-resnet-2015]: He et al., "Deep Residual Learning for Image Recognition," CVPR 2016
[^ba-layernorm-2016]: Ba et al., "Layer Normalization," arXiv 2016
[^rumelhart-backprop-1986]: Rumelhart, Hinton, Williams, "Learning representations by back-propagating errors," Nature 1986
[^hastie-esl-2009]: Hastie, Tibshirani, Friedman, "The Elements of Statistical Learning," Springer 2009
[^nakkiran-double-descent-2019]: Nakkiran et al., "Deep Double Descent," ICLR 2020
[^srivastava-dropout-2014]: Srivastava et al., "Dropout: A Simple Way to Prevent Neural Networks from Overfitting," JMLR 2014
[^loshchilov-decoupled-2019]: Loshchilov and Hutter, "Decoupled Weight Decay Regularization," ICLR 2019
[^kingma-adam-2015]: Kingma and Ba, "Adam: A Method for Stochastic Optimization," ICLR 2015
[^loshchilov-cosine-2017]: Loshchilov and Hutter, "SGDR: Stochastic Gradient Descent with Warm Restarts," ICLR 2017
[^hu-minicpm-2024]: Hu et al., "MiniCPM: Unveiling the Potential of Small Language Models," arXiv 2024
[^sennrich-bpe-2016]: Sennrich, Haddow, Birch, "Neural Machine Translation of Rare Words with Subword Units," ACL 2016
[^kudo-sentencepiece-2018]: Kudo and Richardson, "SentencePiece: A simple and language independent subword tokenizer," EMNLP 2018
[^mikolov-word2vec-2013]: Mikolov et al., "Efficient Estimation of Word Representations in Vector Space," ICLR 2013
[^holtzman-nucleus-2020]: Holtzman et al., "The Curious Case of Neural Text Degeneration," ICLR 2020
[^press-alibi-2022]: Press et al., "Train Short, Test Long: Attention with Linear Biases," ICLR 2022
[^liu-lost-middle-2023]: Liu et al., "Lost in the Middle: How Language Models Use Long Contexts," TACL 2024
[^dao-flashattn-2022]: Dao et al., "FlashAttention: Fast and Memory-Efficient Exact Attention," NeurIPS 2022
[^grinsztajn-trees-tabular-2022]: Grinsztajn et al., "Why do tree-based models still outperform deep learning on tabular data?" NeurIPS 2022
[^lloyd-kmeans-1982]: Lloyd, "Least squares quantization in PCM," IEEE Trans. Info Theory 1982
[^bellman-1961]: Bellman, "Adaptive Control Processes," Princeton University Press 1961
[^manning-ir-2008]: Manning, Raghavan, Schutze, "Introduction to Information Retrieval," Cambridge 2008
[^jurafsky-slp-2023]: Jurafsky and Martin, "Speech and Language Processing," 3rd ed. draft, 2023
[^papineni-bleu-2002]: Papineni et al., "BLEU: a Method for Automatic Evaluation of Machine Translation," ACL 2002
[^lin-rouge-2004]: Lin, "ROUGE: A Package for Automatic Evaluation of Summaries," ACL Workshop 2004
[^zheng-lmsys-2023]: Zheng et al., "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena," NeurIPS 2023
[^murphy-mlpp-2012]: Murphy, "Machine Learning: A Probabilistic Perspective," MIT Press 2012
[^kohavi-trustworthy-2020]: Kohavi, Tang, Xu, "Trustworthy Online Controlled Experiments," Cambridge 2020
[^robertson-bm25-2009]: Robertson and Zaragoza, "The Probabilistic Relevance Framework: BM25 and Beyond," Foundations and Trends in IR, 2009
[^karpukhin-dpr-2020]: Karpukhin et al., "Dense Passage Retrieval for Open-Domain Question Answering," EMNLP 2020
[^sutton-rl-2018]: Sutton and Barto, "Reinforcement Learning: An Introduction," 2nd ed., MIT Press 2018
[^ouyang-instructgpt-2022]: Ouyang et al., "Training language models to follow instructions with human feedback," NeurIPS 2022
[^rafailov-dpo-2023]: Rafailov et al., "Direct Preference Optimization," NeurIPS 2023
[^dosovitskiy-vit-2021]: Dosovitskiy et al., "An Image is Worth 16x16 Words," ICLR 2021
