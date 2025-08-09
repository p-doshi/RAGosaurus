# 🦖 RAGosaurus

**Thank you for stalking :)**

> _“Why build another RAG pipeline?”_  
> Because none of them let you *just point at a GitHub repo and start asking questions like it's Stack Overflow.*  

## 🚨 Problem?

No plug-and-play RAG pipeline that eats codebases and spits out coherent answers — unless you want to deploy LLaMA on a $30,000 GPU cluster.  

## ✅ Solution?

### 🦖 **RAGosaurus** — a lightweight but functional notebook implementation where you:
1. Point it at a folder with Python code, and  
2. Magically start asking questions like “how does `read_csv()` work?”  

### Pros?
- You don’t need an RTX 4090.  
- Works on dusty GPUs with just **6GB VRAM** (tested on 1660 Ti).  
- Actually generates useful answers.  
- No over-engineered LangChain spaghetti.
- SBERT + FAISS options for retrieval — because variety is the spice of life.

---

## 🔧 How It Works

- 🧠 **Encoders**
- CodeT5: Salesforce/codet5-base for code-aware embeddings.
- SBERT (optional): all-MiniLM-L6-v2 for semantic code/document matching.
- Custom pooling: Mean-pooled last hidden states with attention mask weighting for more stable embeddings.  
- 📦 **Retriever:** Retriever
  -Flat L2 index for fast similarity search, persisted to disk.
  -SBERT pipeline: Cosine similarity search over precomputed normalized embeddings.
- ✍️ **Generator:** [`microsoft/phi-2`](https://huggingface.co/microsoft/phi-2) generates the final answers.

Yes, we handle token truncation and all that windowing magic behind the scenes.

### Chunking & Indexing
RAGosaurus uses token-aware overlapping chunking to preserve context in code.
- **Tokenizer**: Salesforce/codet5-base
- **Max tokens**: 500 per chunk, stride 100
- **Overlap**: Ensures function/class definitions aren’t split mid-body.
- **Caching**: Chunks and embeddings are cached to disk for instant reuse.
- **Batch embeddings**: 32-chunk batches to minimize GPU memory usage.
- **Indexing**: FAISS flat L2 index for fast retrieval, persisted to disk.

This design keeps the pipeline fast (<X ms retrieval on 6 GB GPU) while maintaining high retrieval relevance.


---

## 🤖 Example Usage

```python
query = "How does pandas read a CSV file?"
query_emb = embed_query(query, embed_tokenizer, embed_model)

# FAISS retrieval
chunks = retrieve_top_k(query_emb, index, code_chunks, k=5)

# Generate answer
answer = generate_answer(query, chunks, gen_tokenizer, gen_pipe)
print(answer)

```

---

# 🧪 What You Can Play With
- Swap FAISS with SBERT if you're into semantic vibes

- Adjust top_k retrieved chunks (2, 5, 10, 420?)

- Tweak prompt templates or more verbose or terse answers.

- Compare baseline vs.reranked retrieval quality.

---

# 🔍 Smart-Sounding Observations
SBERT retrieval can pull in semantically richer chunks, but sometimes overshoots.

FAISS + CodeT5 embeddings excel at syntax-aware matches.

Fetching too many chunks (k > 10) turns output into token soup.

Phi-2 is surprisingly good for technical queries — just feed it concise context.

---

## 🛠️ Yes, It Uses LangChain — But Not in the Way That Sucks

You’ll see LangChain imports — and that’s fine.  
But this ain’t your typical bloated LangChain pipeline:

- ✅ Used **only** for simple wrappers like retrievers, prompt templates, and huggingface pipelines
- ❌ No agents, tool runners, chain nesting madness
- 🧠 Most of the logic (like chunking, encoding, inference) is **manually wired**
- ⚒️ Think: LangChain *as a toolbox*, not a religion
---

## 🧑‍💻 Setup

git clone https://github.com/p-doshi/RAGosaurus.git

cd RAGosaurus

---

## 📦 Requirements
- Python 3.10+
- PyTorch (with or without CUDA)
- transformers
- sentence-transformers
- faiss
- tqdm

GPU optional (but helps)



GPU optional, but helpful
---

## ✍️ Citation (Not That Anyone Ever Does)
Doshi, P. (2025). RAGosaurus: Retrieval-Augmented Generation for Codebases. GitHub.
---

## 🧃 P.S.
Still a WIP — PRs, stars, or salty issues are welcome.
Also, if you improve this, I’m definitely stealing your code. Cheers.
