# LlamaIndex RAG Chatbot

## 📖 Overview

**LlamaIndex RAG Chatbot** is a reference implementation of a Retrieval‑Augmented Generation (RAG) chat interface built on top of **[LlamaIndex](https://github.com/run-llama/llama_index)**. It demonstrates how to combine a vector store, a language model, and a simple web UI to create a conversational agent that can answer questions using both its internal knowledge and an external document corpus.

The project is deliberately lightweight so that developers can:
- **Understand** the end‑to‑end RAG pipeline.
- **Experiment** with different LLMs, embeddings, and vector stores.
- **Extend** the bot with custom data sources, retrieval strategies, or UI components.

---

## ✨ Features

- **Modular architecture** – each stage (ingest, index, retrieve, generate) lives in its own module.
- **Pluggable components** – swap out the LLM, embedding model, or vector store with a single line change.
- **FastAPI backend** exposing REST endpoints for ingestion and chat.
- **React/Next.js front‑end** (or a simple Streamlit UI) for interactive conversations.
- **Docker support** – run the whole stack with `docker compose`.
- **Extensive type hints & docstrings** for IDE autocompletion.
- **Test suite** covering the core RAG workflow.

---

## 🏗️ Architecture

```
+-------------------+      +-------------------+      +-------------------+
|   Document Store  | ---> |   LlamaIndex      | ---> |   LLM (LLama,   |
| (PDF, TXT, …)     |      |   (Vector Index)  |      |   OpenAI, …)    |
+-------------------+      +-------------------+      +-------------------+
          |                         |                         |
          |                         |                         |
          v                         v                         v
   Ingestion script          Retrieval step          Generation step
```

1. **Ingestion** – Documents are loaded, chunked, and embedded using the chosen embedding model.
2. **Indexing** – Embeddings are stored in a vector DB (e.g., **FAISS**, **Chroma**, **Pinecone**).
3. **Retrieval** – For each user query, the top‑k most relevant chunks are fetched.
4. **Generation** – The retrieved context is passed to the LLM with a prompt template that instructs it to answer concisely.
5. **Chat API** – The backend returns the generated answer together with the source citations.

---

## 📦 Prerequisites

| Requirement | Recommended version |
|-------------|----------------------|
| Python      | `>=3.9`              |
| pip         | latest               |
| Docker      | optional (for containerised run) |
| LLM API key | OpenAI, Anthropic, or a local model endpoint |

---

## ⚙️ Installation

### 1️⃣ Clone the repository
```bash
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot
```

### 2️⃣ Create a virtual environment
```bash
python -m venv .venv
source .venv/bin/activate   # on Windows: .venv\Scripts\activate
```

### 3️⃣ Install dependencies
```bash
pip install -r requirements.txt
```
> The `requirements.txt` pins LlamaIndex, FastAPI, and the chosen vector store libraries.

### 4️⃣ Set environment variables
Create a `.env` file at the project root:
```dotenv
# LLM configuration (choose one)
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=...
# Vector store configuration (if using remote service)
CHROMA_ENDPOINT=http://localhost:8000
```
> For a local model, set `LLM_ENDPOINT` to the inference server URL.

### 5️⃣ (Optional) Run with Docker
```bash
docker compose up --build
```
The API will be available at `http://localhost:8000` and the UI at `http://localhost:3000`.

---

## 🚀 Quick Start

#### 1️⃣ Ingest a sample corpus
```bash
python scripts/ingest.py --source data/sample_documents/
```
This will:
- Load all `*.pdf`, `*.txt`, and `*.md` files in the folder.
- Chunk them (default: 500 tokens).
- Create embeddings and store them in the vector DB.

#### 2️⃣ Start the FastAPI server
```bash
uvicorn app.main:app --reload
```
#### 3️⃣ Chat via the UI or curl
```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"query": "What is Retrieval‑Augmented Generation?"}'
```
You will receive a JSON response containing:
- `answer`: the LLM‑generated reply.
- `sources`: list of document IDs and snippets used for grounding.

---

## 🛠️ Development

### Running the test suite
```bash
pytest -v
```
### Adding a new vector store
1. Install the client library (e.g., `pip install weaviate-client`).
2. Implement a subclass of `BaseVectorStore` in `llamaindex_rag/vectorstores/`.
3. Register it in `app.config` and update the `.env` with the connection details.

### Extending the prompt template
Edit `llamaindex_rag/prompts.py`. The default template is:
```jinja
You are a helpful assistant. Answer the user's question using only the provided context. Cite sources with brackets like [1].

Context:
{{retrieved_chunks}}

Question: {{query}}
```
Feel free to add system messages, temperature settings, or chain multiple LLM calls.

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:
1. Fork the repository.
2. Create a feature branch (`git checkout -b feat/your-feature`).
3. Write tests for new functionality.
4. Ensure `black` and `flake8` pass (`make lint`).
5. Open a Pull Request with a clear description of the change.

See `CONTRIBUTING.md` for detailed guidelines.

---

## 📄 License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## 📚 Further Reading
- LlamaIndex documentation: https://docs.llamaindex.ai/
- Retrieval‑Augmented Generation primer: https://arxiv.org/abs/2005.11401
- FastAPI tutorial: https://fastapi.tiangolo.com/tutorial/
