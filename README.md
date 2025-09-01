# LlamaIndex RAG Chatbot

**LlamaIndex RAG Chatbot** is a minimal yet extensible reference implementation that demonstrates how to build a Retrieval‑Augmented Generation (RAG) powered chatbot using **LlamaIndex** (formerly GPT Index) and **LangChain** compatible LLMs. The project showcases:

- Document ingestion (PDF, txt, markdown, etc.)
- Vector store indexing with **FAISS** (plug‑and‑play for other stores)
- Real‑time retrieval and generation loop
- Simple FastAPI web‑socket interface for interactive chat
- Docker support for reproducible deployments

---

## Table of Contents

1. [Features](#features)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Running the Bot](#running-the-bot)
5. [Project Structure](#project-structure)
6. [How It Works](#how-it-works)
7. [Configuration](#configuration)
8. [Testing](#testing)
9. [Contributing](#contributing)
10. [License](#license)

---

## Features

- **RAG pipeline** built on LlamaIndex's `Retriever` and `LLMChain`
- **Modular document loaders** – add new formats by extending `loaders/`
- **Pluggable vector stores** – default FAISS, easy swap to Pinecone, Weaviate, etc.
- **FastAPI WebSocket chat UI** – minimal front‑end for quick demos
- **Dockerfile & docker‑compose** – one‑command local deployment
- **Extensive type hints & docstrings** – IDE‑friendly development

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Python      | >=3.9   |
| pip         | latest  |
| Docker (optional) | >=20.10 |

> **Note**: The code is tested with Python 3.11 and `torch` CPU builds. For GPU acceleration install the appropriate `torch` wheel.

---

## Installation

```bash
# Clone the repository
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate   # on Windows use `.venv\Scripts\activate`

# Install dependencies
pip install -r requirements.txt
```

### Optional: Install with Docker

```bash
# Build the image
docker build -t llamaindex-rag-chatbot .

# Run the container (exposes port 8000)
docker run -p 8000:8000 llamaindex-rag-chatbot
```

---

## Running the Bot

### 1️⃣ Index your documents

Place any documents you want the bot to know inside the `data/` directory. Then run:

```bash
python scripts/build_index.py
```

The script will:
- Load supported files (`.pdf`, `.txt`, `.md`)
- Split them into chunks (default 500 tokens)
- Embed using the LLM‑provided embedding model (OpenAI, Cohere, etc.)
- Persist the FAISS index to `index/faiss_index`

### 2️⃣ Start the API server

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

Open your browser at `http://localhost:8000` – the minimal UI will load and you can start chatting.

---

## Project Structure

```
llamaindex-rag-chatbot/
├─ app/                     # FastAPI application
│   ├─ main.py              # Entry point (WebSocket routes)
│   └─ schemas.py           # Pydantic models
├─ data/                    # <-- place your source documents here
├─ index/                   # Persisted vector store
├─ loaders/                 # Custom document loaders (extendable)
│   └─ pdf_loader.py
├─ scripts/                 # Helper scripts (index building, testing)
│   └─ build_index.py
├─ tests/                   # Unit / integration tests
│   └─ test_chatbot.py
├─ .env.example             # Sample environment variables
├─ Dockerfile
├─ requirements.txt
└─ README.md                # ← you are here
```

---

## How It Works

1. **Document Loading** – LlamaIndex's `SimpleDirectoryReader` (or a custom loader) reads files from `data/`.
2. **Chunking & Embedding** – Text is split into manageable chunks; each chunk is embedded via the configured embedding model.
3. **Vector Store** – Embeddings are stored in FAISS (or another vector DB). The index is persisted for fast reloads.
4. **Retriever** – At query time, the retriever fetches the top‑k relevant chunks.
5. **LLM Generation** – The retrieved context is passed to the LLM (e.g., `gpt‑3.5‑turbo`) using an `LLMChain` that formats a prompt like:
   ```
   Context: <retrieved documents>
   Question: <user query>
   Answer:
   ```
6. **FastAPI WebSocket** – The front‑end sends user messages; the back‑end runs the RAG pipeline and streams the answer back.

---

## Configuration

All configurable values live in a `.env` file (copy `.env.example` to `.env`). Key variables:

| Variable | Description | Example |
|----------|-------------|---------|
| `LLM_MODEL` | Name of the LLM to use (OpenAI, Anthropic, etc.) | `gpt-3.5-turbo` |
| `EMBEDDING_MODEL` | Embedding provider/model | `text-embedding-ada-002` |
| `FAISS_INDEX_PATH` | Path where the FAISS index is stored | `index/faiss_index` |
| `CHUNK_SIZE` | Number of tokens per chunk | `500` |
| `TOP_K` | Number of retrieved documents per query | `4` |
| `API_KEY` | API key for the chosen LLM/embedding service | `sk-...` |

You can also override these values programmatically via the `settings.py` module.

---

## Testing

```bash
pytest -s tests/
```

The test suite covers:
- Document loading sanity checks
- Index building and persistence
- End‑to‑end chat flow using a mock LLM
- WebSocket connectivity

---

## Contributing

Contributions are welcome! Follow these steps:

1. **Fork the repository** and clone your fork.
2. **Create a feature branch** (`git checkout -b feat/your-feature`).
3. **Write tests** for new functionality.
4. **Run the full test suite** (`pytest`).
5. **Submit a Pull Request** with a clear description and reference to any related issue.

### Code Style
- Use **black** for formatting (`black .`).
- Type hints are mandatory for public functions.
- Follow the existing folder layout for new modules.

---

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## Acknowledgements

- **LlamaIndex** – powerful data framework for LLM‑aware retrieval.
- **FastAPI** – asynchronous web framework that makes real‑time chat simple.
- **FAISS** – efficient similarity search library.

---

*Happy hacking! 🚀*