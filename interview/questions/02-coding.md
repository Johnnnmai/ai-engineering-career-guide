# Coding and Implementation Questions

AI Engineer coding rounds split into two flavors: traditional algorithm/DS rounds (45-60 min, LeetCode-style but often ML-flavored) and ML coding rounds (45-90 min, build something practical). Some companies combine both into a single longer session. I found the ratio is roughly 60/40 in favor of the practical ML coding round at AI-focused companies, while big tech leans heavier on traditional DSA.


## Part 1: Algorithm and Data Structure Rounds

### Arrays and Strings (ML-flavored)

Q: Given a list of document embeddings and a query embedding, find the top-k most similar documents by cosine similarity. Do not use any ML libraries.

This tests both your understanding of cosine similarity and basic algorithm skills. Cosine similarity = dot product / (norm_a * norm_b). Compute similarity for all documents, then use a min-heap of size k to track the top-k results in O(n log k) time instead of sorting all n results in O(n log n). Edge cases: zero vectors (divide by zero), empty lists, k > n. [^leetcode-top-k]

```python
import heapq
import math

def top_k_similar(query, documents, k):
    def cosine_sim(a, b):
        dot = sum(x * y for x, y in zip(a, b))
        norm_a = math.sqrt(sum(x * x for x in a))
        norm_b = math.sqrt(sum(x * x for x in b))
        if norm_a == 0 or norm_b == 0:
            return 0.0
        return dot / (norm_a * norm_b)

    heap = []
    for i, doc in enumerate(documents):
        sim = cosine_sim(query, doc)
        if len(heap) < k:
            heapq.heappush(heap, (sim, i))
        elif sim > heap[0][0]:
            heapq.heapreplace(heap, (sim, i))

    return sorted(heap, key=lambda x: -x[0])
```

Follow-up: How would you handle 10 million documents?

Use approximate nearest neighbor search (FAISS with IVF or HNSW index). Exact search is O(n*d) per query which is too slow. ANN trades a small amount of recall for orders-of-magnitude speedup. Also consider dimensionality reduction (PCA to reduce d) and quantization (product quantization to reduce memory). [^johnson-faiss-2019]

Q: Implement a sliding window tokenizer that splits text into overlapping chunks of a given token size with a given overlap.

```python
def sliding_window_chunk(tokens, chunk_size, overlap):
    if chunk_size <= 0 or overlap >= chunk_size:
        raise ValueError("Invalid chunk_size or overlap")
    chunks = []
    step = chunk_size - overlap
    for i in range(0, len(tokens), step):
        chunk = tokens[i:i + chunk_size]
        if chunk:
            chunks.append(chunk)
        if i + chunk_size >= len(tokens):
            break
    return chunks
```

This comes up because chunking is fundamental to RAG systems. Follow-ups typically probe edge cases: what if the last chunk is very small? Should you pad it, drop it, or merge it with the previous chunk? In production, I found that dropping chunks shorter than 50% of `chunk_size` works well.


### Trees and Graphs

Q: Given a taxonomy of product categories as a tree, find all categories that match a search query and return them with their full path. This is relevant to e-commerce recommendation systems.

This is a tree traversal with path tracking. DFS with a path accumulator, checking each node against the query. Time complexity O(n) where n is total nodes. [^cormen-clrs-2009]

Q: You have a DAG representing a data pipeline (preprocessing -> feature extraction -> model training -> evaluation). Implement topological sort to determine execution order.

Standard Kahn's algorithm (BFS with in-degree tracking) or DFS-based topological sort. This comes up in ML pipeline orchestration (Airflow, Prefect, Dagster all use DAG-based execution). [^cormen-clrs-2009]

```python
from collections import deque, defaultdict

def topological_sort(graph):
    in_degree = defaultdict(int)
    for node in graph:
        for neighbor in graph[node]:
            in_degree[neighbor] += 1

    queue = deque([n for n in graph if in_degree[n] == 0])
    result = []

    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    if len(result) != len(graph):
        raise ValueError("Cycle detected")
    return result
```


### Dynamic Programming

Q: Given a budget B and a list of ML models, each with a cost and an accuracy score, select models to maximize total accuracy without exceeding the budget. (This is the 0/1 knapsack problem dressed up in ML clothes.)

Standard DP knapsack. Time O(n * B), space O(B) with rolling array optimization. Companies like to frame classic DP problems as resource allocation or model selection scenarios. [^cormen-clrs-2009]

Q: Given a sequence of token IDs, find the longest common subsequence with a reference sequence. (Relevant to evaluating generation quality.)

Classic LCS with DP table. Time and space O(m * n), reducible to O(min(m,n)) space. This is the algorithmic foundation of ROUGE-L metric. [^lin-rouge-2004]


### String/Text Processing

Q: Implement a simple trie-based autocomplete system for search suggestions.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.suggestions = []

class Autocomplete:
    def __init__(self, phrases):
        self.root = TrieNode()
        for phrase in phrases:
            self._insert(phrase)

    def _insert(self, phrase):
        node = self.root
        for char in phrase.lower():
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
            if len(node.suggestions) < 5:
                node.suggestions.append(phrase)

    def search(self, prefix):
        node = self.root
        for char in prefix.lower():
            if char not in node.children:
                return []
            node = node.children[char]
        return node.suggestions
```


## Part 2: ML Coding Rounds

### Implement Attention From Scratch

This is the single most common ML coding question. You should be able to write it from memory.

Q: Implement scaled dot-product attention and multi-head attention in NumPy or PyTorch.

```python
import numpy as np

def scaled_dot_product_attention(Q, K, V, mask=None):
    d_k = Q.shape[-1]
    scores = Q @ K.transpose(-2, -1) / np.sqrt(d_k)
    if mask is not None:
        scores = np.where(mask == 0, -1e9, scores)
    weights = softmax(scores, axis=-1)
    return weights @ V

def softmax(x, axis=-1):
    e_x = np.exp(x - np.max(x, axis=axis, keepdims=True))
    return e_x / e_x.sum(axis=axis, keepdims=True)

def multi_head_attention(Q, K, V, W_q, W_k, W_v, W_o, num_heads):
    batch_size, seq_len, d_model = Q.shape
    d_k = d_model // num_heads

    Q = Q @ W_q
    K = K @ W_k
    V = V @ W_v

    Q = Q.reshape(batch_size, seq_len, num_heads, d_k).transpose(0, 2, 1, 3)
    K = K.reshape(batch_size, seq_len, num_heads, d_k).transpose(0, 2, 1, 3)
    V = V.reshape(batch_size, seq_len, num_heads, d_k).transpose(0, 2, 1, 3)

    attn_output = scaled_dot_product_attention(Q, K, V)

    attn_output = attn_output.transpose(0, 2, 1, 3).reshape(
        batch_size, seq_len, d_model
    )
    return attn_output @ W_o
```

Follow-ups: Add causal masking. Explain why you divide by sqrt(d_k). What happens if you skip the scaling? (Dot products grow with dimension, pushing softmax into saturation where gradients vanish.) [^vaswani-2017]


### Write a Simple Neural Network

Q: Implement a two-layer neural network for binary classification using only NumPy. Include forward pass, loss computation, and backpropagation.

```python
import numpy as np

class TwoLayerNet:
    def __init__(self, input_dim, hidden_dim, lr=0.01):
        self.W1 = np.random.randn(input_dim, hidden_dim) * 0.01
        self.b1 = np.zeros(hidden_dim)
        self.W2 = np.random.randn(hidden_dim, 1) * 0.01
        self.b2 = np.zeros(1)
        self.lr = lr

    def sigmoid(self, z):
        return 1 / (1 + np.exp(-np.clip(z, -500, 500)))

    def forward(self, X):
        self.z1 = X @ self.W1 + self.b1
        self.a1 = np.maximum(0, self.z1)  # ReLU
        self.z2 = self.a1 @ self.W2 + self.b2
        self.a2 = self.sigmoid(self.z2)
        return self.a2

    def compute_loss(self, y_pred, y_true):
        eps = 1e-8
        return -np.mean(
            y_true * np.log(y_pred + eps)
            + (1 - y_true) * np.log(1 - y_pred + eps)
        )

    def backward(self, X, y_true):
        m = X.shape[0]
        dz2 = self.a2 - y_true.reshape(-1, 1)
        dW2 = self.a1.T @ dz2 / m
        db2 = np.mean(dz2, axis=0)

        da1 = dz2 @ self.W2.T
        dz1 = da1 * (self.z1 > 0)  # ReLU derivative
        dW1 = X.T @ dz1 / m
        db1 = np.mean(dz1, axis=0)

        self.W2 -= self.lr * dW2
        self.b2 -= self.lr * db2
        self.W1 -= self.lr * dW1
        self.b1 -= self.lr * db1
```


### Tokenizer Implementation

Q: Implement a basic BPE (Byte Pair Encoding) tokenizer.

```python
from collections import Counter

def get_pair_counts(corpus):
    pairs = Counter()
    for word, freq in corpus.items():
        symbols = word.split()
        for i in range(len(symbols) - 1):
            pairs[(symbols[i], symbols[i + 1])] += freq
    return pairs

def merge_pair(pair, corpus):
    merged = {}
    bigram = " ".join(pair)
    replacement = "".join(pair)
    for word, freq in corpus.items():
        new_word = word.replace(bigram, replacement)
        merged[new_word] = freq
    return merged

def train_bpe(text, num_merges):
    words = text.split()
    corpus = Counter()
    for word in words:
        corpus[" ".join(list(word)) + " </w>"] += 1

    merges = []
    for i in range(num_merges):
        pairs = get_pair_counts(corpus)
        if not pairs:
            break
        best_pair = max(pairs, key=pairs.get)
        corpus = merge_pair(best_pair, corpus)
        merges.append(best_pair)

    return merges, corpus
```

This is simplified but demonstrates understanding of the core algorithm. Follow-ups: How does this differ from WordPiece? (WordPiece uses likelihood-based scoring instead of frequency.) How does SentencePiece handle languages without whitespace? (It treats the input as a raw byte stream.) [^sennrich-bpe-2016]


### Python-Specific Questions

Q: Explain generators and when you would use them in ML pipelines.

Generators yield values lazily, one at a time, without storing the entire sequence in memory. In ML pipelines, they are essential for data loading: streaming a 100GB dataset from disk, applying transformations on the fly, and feeding batches to the model without loading everything into RAM. PyTorch's `DataLoader` uses a similar pattern internally.

```python
def batch_generator(dataset, batch_size):
    for i in range(0, len(dataset), batch_size):
        batch = dataset[i:i + batch_size]
        yield preprocess(batch)
```

Q: What are decorators? Write a decorator that caches LLM API responses.

```python
import hashlib
import json
import functools

def cache_llm_response(func):
    cache = {}

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        key = hashlib.md5(
            json.dumps({"args": args, "kwargs": kwargs},
                       sort_keys=True, default=str).encode()
        ).hexdigest()
        if key not in cache:
            cache[key] = func(*args, **kwargs)
        return cache[key]

    wrapper.cache = cache
    return wrapper

@cache_llm_response
def call_llm(prompt, model="gpt-4"):
    # actual API call here
    pass
```

Q: Explain the GIL and its implications for ML workloads.

The Global Interpreter Lock prevents multiple Python threads from executing bytecode simultaneously. For CPU-bound ML tasks (data preprocessing, feature engineering), this means threading provides no speedup. Solutions: use multiprocessing (separate processes with separate GILs), NumPy/PyTorch operations (release the GIL internally and use C/CUDA), or async I/O for I/O-bound tasks (API calls, database queries). In practice, ML training offloads compute to C++/CUDA through frameworks, so the GIL rarely bottlenecks model training itself. It mainly affects data loading and preprocessing. [^python-docs-gil]


### Data Processing

Q: Given a messy CSV of user interactions with an AI chatbot, clean and prepare it for analysis. The data has missing values, duplicate rows, inconsistent timestamps, and mixed data types.

```python
import pandas as pd

def clean_chatbot_data(filepath):
    df = pd.read_csv(filepath)

    df = df.drop_duplicates(subset=["session_id", "timestamp", "message"])

    df["timestamp"] = pd.to_datetime(df["timestamp"], errors="coerce")
    df = df.dropna(subset=["timestamp"])

    df["response_time_ms"] = pd.to_numeric(
        df["response_time_ms"], errors="coerce"
    )
    median_rt = df["response_time_ms"].median()
    df["response_time_ms"] = df["response_time_ms"].fillna(median_rt)

    df["message"] = df["message"].str.strip().str.lower()
    df = df[df["message"].str.len() > 0]

    df = df.sort_values(["session_id", "timestamp"]).reset_index(drop=True)

    return df
```

Follow-up: How would you handle this at 100GB scale? Use Dask or Polars for out-of-core processing. Polars is generally faster for single-machine workflows. For truly large scale, use Spark. [^polars-docs]


### API Development

Q: Build a FastAPI endpoint that serves an LLM with streaming responses.

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import asyncio

app = FastAPI()

async def generate_stream(prompt):
    # simulate streaming LLM response
    for chunk in call_llm_streaming(prompt):
        yield f"data: {chunk}\n\n"
        await asyncio.sleep(0)  # yield control
    yield "data: [DONE]\n\n"

@app.post("/chat")
async def chat(request: dict):
    prompt = request.get("prompt", "")
    if not prompt:
        return {"error": "prompt required"}

    return StreamingResponse(
        generate_stream(prompt),
        media_type="text/event-stream",
        headers={
            "Cache-Control": "no-cache",
            "Connection": "keep-alive",
        },
    )
```

Follow-ups: How do you handle authentication? Rate limiting? Timeouts? What if the model hangs? Implement a timeout wrapper. How do you handle concurrent requests with a single GPU? Queue requests and batch them. [^fastapi-docs]


### LLM Integration Coding

Q: Write a function that calls the Claude API with retry logic, structured output parsing, and error handling.

```python
import anthropic
import json
import time

def call_claude_with_retry(
    prompt,
    system="",
    model="claude-sonnet-4-20250514",
    max_retries=3,
    timeout=30,
):
    client = anthropic.Anthropic()

    for attempt in range(max_retries):
        try:
            response = client.messages.create(
                model=model,
                max_tokens=4096,
                system=system,
                messages=[{"role": "user", "content": prompt}],
                timeout=timeout,
            )
            return response.content[0].text

        except anthropic.RateLimitError:
            wait = 2 ** attempt
            time.sleep(wait)
        except anthropic.APIStatusError as e:
            if e.status_code >= 500:
                time.sleep(2 ** attempt)
            else:
                raise
        except anthropic.APITimeoutError:
            if attempt == max_retries - 1:
                raise

    raise Exception("Max retries exceeded")

def extract_json_from_response(text):
    # handle markdown code blocks
    if "```json" in text:
        start = text.index("```json") + 7
        end = text.index("```", start)
        text = text[start:end]
    elif "```" in text:
        start = text.index("```") + 3
        end = text.index("```", start)
        text = text[start:end]

    return json.loads(text.strip())
```


### SQL for AI Engineers

Q: Write a query to find the top 10 users by average conversation length with a chatbot, including only users with at least 5 conversations in the past 30 days.

```sql
SELECT
    user_id,
    COUNT(DISTINCT conversation_id) AS num_conversations,
    AVG(message_count) AS avg_messages_per_conversation,
    AVG(duration_seconds) AS avg_duration_seconds
FROM conversations
WHERE created_at >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY user_id
HAVING COUNT(DISTINCT conversation_id) >= 5
ORDER BY avg_messages_per_conversation DESC
LIMIT 10;
```

Q: Write a query to detect model quality degradation by comparing daily average response ratings over the past 60 days.

```sql
WITH daily_ratings AS (
    SELECT
        DATE(created_at) AS day,
        AVG(user_rating) AS avg_rating,
        COUNT(*) AS num_ratings
    FROM model_responses
    WHERE created_at >= CURRENT_DATE - INTERVAL '60 days'
    GROUP BY DATE(created_at)
)
SELECT
    day,
    avg_rating,
    num_ratings,
    AVG(avg_rating) OVER (
        ORDER BY day ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS rolling_7day_avg
FROM daily_ratings
ORDER BY day;
```


## How to Prepare

Timeline: 3-4 weeks minimum

Week 1-2: DSA fundamentals. Solve 50-75 LeetCode problems focused on arrays, strings, trees, graphs, and dynamic programming. Prioritize medium difficulty. Do at least 10 problems that involve matrices/vectors since they show up in ML-flavored questions.

Week 2-3: ML implementation. Implement attention, a simple neural network, BPE tokenizer, and cosine similarity search from scratch without libraries. Practice until you can write each in under 20 minutes.

Week 3-4: Practical coding. Build a complete FastAPI endpoint that serves an LLM, write data cleaning pipelines with pandas, practice SQL window functions and aggregations. Write at least one end-to-end RAG pipeline using the API of your choice.

Throughout: Practice in a Google Doc or CoderPad (not your IDE). Get comfortable writing code without autocomplete, running it in your head, and debugging by inspection. Time yourself: 25 minutes for DSA, 45 minutes for ML implementation.

Tools to practice with:
- LeetCode (filter by company and topic)
- Exercism Python track (for Python-specific skills)
- HackerRank ML challenges
- Build a small project from scratch each week


## Common Mistakes

Writing code without first clarifying requirements. Always ask: what are the input constraints? What is the expected output format? What edge cases should I handle? Two minutes of clarification saves ten minutes of rewriting.

Jumping to an optimal solution immediately. Start with the brute force approach, explain its complexity, then optimize. This shows your thought process and gives you a working solution to fall back on if time runs short.

Ignoring edge cases. Empty inputs, single elements, duplicates, very large values, negative numbers. Mention these even if you do not code for all of them.

Not testing your code. Walk through your solution with a small example before declaring it done. Interviewers notice when you do not verify your own work.

Over-engineering in ML coding rounds. If asked to build a RAG pipeline, do not spend 30 minutes on error handling and logging. Get the core pipeline working first, then add polish if time permits.

Forgetting Python-specific patterns. Using `range(len(list))` instead of `enumerate`, manual string concatenation instead of `join`, not using list comprehensions where appropriate. These signal inexperience.


## Sources

[^vaswani-2017]: Vaswani et al., "Attention Is All You Need," NeurIPS 2017
[^sennrich-bpe-2016]: Sennrich, Haddow, Birch, "Neural Machine Translation of Rare Words with Subword Units," ACL 2016
[^lin-rouge-2004]: Lin, "ROUGE: A Package for Automatic Evaluation of Summaries," ACL Workshop 2004
[^cormen-clrs-2009]: Cormen, Leiserson, Rivest, Stein, "Introduction to Algorithms," 3rd ed., MIT Press 2009
[^leetcode-top-k]: LeetCode Problem 215: Kth Largest Element in an Array (and variants)
[^johnson-faiss-2019]: Johnson, Douze, Jegou, "Billion-scale similarity search with GPUs," IEEE Trans. Big Data 2019
[^python-docs-gil]: Python Documentation, "Thread State and the Global Interpreter Lock"
[^polars-docs]: Polars Documentation, https://pola.rs
[^fastapi-docs]: FastAPI Documentation, https://fastapi.tiangolo.com
