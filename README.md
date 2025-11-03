# LlamaIndex RAG Chatbot

## 📖 Overview

**LlamaIndex RAG Chatbot** is a reference implementation that demonstrates how to build a Retrieval‑Augmented Generation (RAG) powered chatbot using the **LlamaIndex** ecosystem. The bot combines a vector store for document retrieval with a large language model (LLM) to generate context‑aware responses, enabling developers to quickly prototype knowledge‑base assistants, support agents, or any conversational AI that needs up‑to‑date factual grounding.

---

## 🚀 Features

- **RAG pipeline** built on LlamaIndex (`llama_index`), supporting both synchronous and async workflows.
- **Pluggable vector stores** – out‑of‑the‑box support for FAISS, Chroma, Pinecone, and others.
- **Configurable LLM back‑ends** – works with OpenAI, Anthropic, Llama‑CPP, and any OpenAI‑compatible API.
- **Simple CLI** for rapid testing and a **FastAPI** server for integration into web or mobile front‑ends.
- **Dockerised** development environment for reproducible builds.
- **Extensible** – add custom loaders, text splitters, or post‑processing steps with minimal code changes.

---

## 🛠️ Installation

### Prerequisites

- Python **3.9** or newer.
- Access to an LLM API key (e.g., `OPENAI_API_KEY`).
- Optional: Docker & Docker‑Compose if you prefer containerised execution.

### Quick install (local)

```bash
# Clone the repository
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment
python -m venv .venv
source .venv/bin/activate  # on Windows use `.venv\Scripts\activate`

# Install dependencies
pip install -r requirements.txt
```

### Docker (optional)

```bash
# Build the image
docker build -t llamaindex-rag-chatbot .

# Run the container (replace <YOUR_API_KEY> with your actual key)
docker run -e OPENAI_API_KEY=<YOUR_API_KEY> -p 8000:8000 llamaindex-rag-chatbot
```

---

## ⚙️ Configuration

All configuration values are read from environment variables or a `.env` file placed at the project root. The most common variables are:

| Variable | Description | Example |
|----------|-------------|---------|
| `OPENAI_API_KEY` | API key for OpenAI (or compatible) LLMs | `sk-...` |
| `LLM_MODEL` | Model name to use (default: `gpt-4o-mini`) | `gpt-3.5-turbo` |
| `VECTOR_STORE` | Vector store backend (`faiss`, `chroma`, `pinecone`, …) | `faiss` |
| `CHUNK_SIZE` | Number of tokens per document chunk | `512` |
| `CHUNK_OVERLAP` | Overlap between chunks to preserve context | `50` |
| `TOP_K` | Number of retrieved documents per query | `5` |

Create a `.env.example` file for reference and copy it to `.env` before running the app.

---

## 📚 Usage

### 1️⃣ Index your knowledge base

```python
from llama_index import SimpleDirectoryReader, GPTVectorStoreIndex

# Load documents from a folder (supports txt, pdf, md, …)
documents = SimpleDirectoryReader('data').load_data()

# Build the index – you can switch the vector store via the `vector_store` argument
index = GPTVectorStoreIndex.from_documents(
    documents,
    embed_model="openai",
    vector_store="faiss"
)

# Persist the index for later use
index.storage_context.persist(persist_dir="./storage")
```

### 2️⃣ Run the chatbot (CLI)

```bash
python -m llamaindex_rag_chatbot.cli --index-dir ./storage
```

You will be dropped into an interactive prompt:

```
> Hello, how can I help you?
> 
```

### 3️⃣ Run the FastAPI server

```bash
uvicorn llamaindex_rag_chatbot.api:app --host 0.0.0.0 --port 8000
```

**Endpoint**: `POST /chat`

```json
{
  "message": "Explain the difference between retrieval‑augmented generation and standard LLM generation."
}
```

**Response**:

```json
{
  "answer": "Retrieval‑augmented generation ...",
  "sources": ["doc1.pdf", "doc2.txt"]
}
```

---

## 🤝 Contributing

Contributions are welcome! Follow these steps:

1. **Fork** the repository.
2. **Create a branch** for your feature or bugfix:
   ```bash
   git checkout -b feature/awesome‑feature
   ```
3. **Write tests** (we use `pytest`). Ensure coverage stays above 80 %.
4. **Run the test suite**:
   ```bash
   pytest -q
   ```
5. **Submit a Pull Request** with a clear description of the change.

### Development workflow

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run linting & type checking
ruff check .
pyright .
```

Please keep the code style consistent with the existing project (PEP 8, type hints, and docstrings).

---

## 📄 License

This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- **LlamaIndex** – the core library that makes building RAG pipelines straightforward.
- **OpenAI**, **Anthropic**, **Llama‑CPP** – for providing powerful LLM APIs.
- Community contributors who help improve the example and keep the docs up‑to‑date.

---

*Happy coding!*
