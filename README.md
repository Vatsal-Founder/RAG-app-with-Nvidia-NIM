# RAG App with NVIDIA NIM — Streamlit + LangChain

An end‑to‑end **RAG** application that performs fast, grounded Q\&A over **any pdf you upload**, powered by **NVIDIA NIM** for low‑latency inference and **LangChain** for orchestration.

> Repo: `Vatsal-Founder/RAG-app-with-Nvidia-NIM` (Python, GPL‑3.0) — key files include `RAGnvidia.py` and `requirements.txt`.

---

## Features

* 📄 **Upload PDF research papers** and build a local vector index.
* 🔎 **Retrieve relevant passages** and generate **grounded answers** with citations.
* ⚡ **NVIDIA NIM** for fast chat/LLM inference via the NVIDIA API key.
* 🧱 **LangChain pipeline**: loader → splitter → embeddings → vector store → retriever → LLM.
* 🖥️ **Streamlit UI** for a simple, shareable demo.

Link: 
---

## Architecture

```
[Streamlit UI]
   ├─ Upload PDF(s)
   ├─ Ask questions
   ↓
[LangChain RAG]
   ├─ Load & split documents (chunks)
   ├─ Embed chunks (NIM or provider in code)
   ├─ Vector store (local FAISS or configured store)
   ├─ Retrieve top‑k passages
   └─ Generate answer (NVIDIA NIM chat model) → citations
```

---

## Requirements

* Python **3.10+**
* **NVIDIA API key** (for NIM)

---

## Quick Start

### 1) Install

```bash
git clone https://github.com/Vatsal-Founder/RAG-app-with-Nvidia-NIM.git
cd RAG-app-with-Nvidia-NIM
python -m venv .venv && source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env  # if present, or create your own
```

### 2) Configure environment

Create a `.env` file with at least:

```ini
# NVIDIA NIM
NVIDIA_API_KEY=your_nvidia_api_key
# Example model names (adjust to match your code)
NVIDIA_CHAT_MODEL=meta/llama-3.1-8b-instruct
NVIDIA_EMBEDDINGS_MODEL=nvidia/nv-embedqa-e5-v5

# Retrieval defaults (optional)
CHUNK_SIZE=1000
CHUNK_OVERLAP=150
TOP_K=5
```

> If your implementation sets model names directly in code, you can omit the model envs.

### 3) Run

```bash
streamlit run RAGnvidia.py
```

Open [http://localhost:8501](http://localhost:8501), upload a PDF, and start asking questions.

---

## Using the App

1. **Upload** one or more PDFs (research papers).
2. The app **splits** text, creates **embeddings**, and **indexes** them locally.
3. Ask a question. The retriever fetches the most relevant chunks; the NIM‑backed LLM composes an answer with **citations/snippets**.

> Tip: For dense, technical papers, chunk size ≈ **800–1200** with **120–200** overlap yields stable retrieval.

---

## Deployment (Optional)

### Docker

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["streamlit", "run", "RAGnvidia.py", "--server.port", "8501", "--server.address", "0.0.0.0"]
```

Build & run:

```bash
docker build -t rag-nim .
docker run -p 8501:8501 --env-file .env rag-nim
```

### PaaS

* Command: `streamlit run RAGnvidia.py --server.port $PORT --server.address 0.0.0.0`
* Set `NVIDIA_API_KEY` in your platform’s env/secrets.

---

## Configuration Tips

* **Models**: choose a fast instruct model for chat; pick an embeddings model optimized for retrieval.
* **Top‑k**: start with 5; increase if answers miss context.
* **Citations**: surface source chunk/page numbers in the UI for transparency.

---

