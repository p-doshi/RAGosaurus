# 🦖 RAGosaurus

**Thank you for stalking :)**

> _“Why build another RAG pipeline?”_  
> Because none of them let you *just point at a GitHub repo and start asking questions like it's Stack Overflow.*  

## 🚨 Problem?

No plug-and-play RAG pipeline that eats codebases and spits out coherent answers — unless you want to deploy LLaMA on a $30,000 GPU cluster.  

## ✅ Solution?

### 🦖 **RAGosaurus** — a janky but functional notebook implementation where you:
1. Point it at a folder with Python code, and  
2. Magically start asking questions like “how does `read_csv()` work?”  

### Pros?
- You don’t need an RTX 4090.  
- Works on dusty GPUs with just **6GB VRAM** (tested on 1660 Ti).  
- Actually generates useful answers.  
- No over-engineered LangChain spaghetti.  

---

## 🔧 How It Works

- 🧠 **Encoder:** [`Salesforce/codet5-base`](https://huggingface.co/Salesforce/codet5-base) handles code chunk embeddings.  
- 📦 **Retriever:** FAISS does vector search. You can also try SBERT.  
- ✍️ **Generator:** [`microsoft/phi-2`](https://huggingface.co/microsoft/phi-2) generates the final answers.

Yes, we handle token truncation and all that windowing magic behind the scenes.



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
Swap FAISS with SBERT if you're into semantic vibes

Adjust top_k retrieved chunks (2, 5, 10, 420?)

Tweak prompt templates

Compare baseline vs. tuned generation

---

# 🔍 Smart-Sounding Observations
SBERT gives more semantically aligned chunks than FAISS (but slower).

Prompt tweaks dramatically affect output quality.

Fetching too many chunks (k > 10) turns answers into token soup.

Phi-2 does well with technical queries — just don’t overwhelm it.

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
Python 3.10+

PyTorch (w/ or w/o CUDA)

transformers, sentence-transformers, faiss, tqdm, etc.

GPU optional, but helpful
---

## ✍️ Citation (Not That Anyone Ever Does)
Doshi, P. (2025). RAGosaurus: Retrieval-Augmented Generation for Codebases. GitHub.
---

## 🧃 P.S.
Still a WIP — PRs, stars, or salty issues are welcome.
Also, if you improve this, I’m definitely stealing your code. Cheers.
