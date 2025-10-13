# LlamaIndex RAG Chatbot

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A **Retrieval‑Augmented Generation (RAG)** chatbot built on top of **[LlamaIndex](https://github.com/run-llama/llama_index)**. The bot combines a vector store with LLM inference to answer user queries with up‑to‑date, source‑grounded information.

---

## Table of Contents

- [Features](#features)
- [Quick Start](#quick-start)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Running the Demo](#running-the-demo)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Indexing Documents](#indexing-documents)
  - [Chatting with the Bot](#chatting-with-the-bot)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- **RAG pipeline** using LlamaIndex's `VectorStoreIndex` and a configurable LLM (OpenAI, Anthropic, Cohere, etc.).
- **Document ingestion** from local files (`.txt`, `.pdf`, `.md`) and optional remote sources (URLs, S3).
- **Source attribution** – each answer is accompanied by citations linking back to the original chunk.
- **Modular architecture** – swap vector stores (FAISS, Chroma, Pinecone) and LLM providers with minimal code changes.
- **Docker support** for reproducible local development.
- **Extensible CLI** and optional FastAPI web interface.

---

## Quick Start

### Prerequisites

- Python **3.9** or newer.
- An LLM API key (e.g., `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`).
- (Optional) `git` and `docker` if you prefer containerised execution.

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

> **Tip**: The project uses `poetry` for dependency management. If you prefer Poetry, run `poetry install` instead of the `pip` command above.

### Running the Demo

1. **Create a `.env` file** at the project root with your LLM credentials:

   ```dotenv
   # Example for OpenAI
   OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx
   
   # If you use Anthropic, add:
   # ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxx
   ```

2. **Index sample documents** (the `data/` folder contains a few markdown files):

   ```bash
   python scripts/index_documents.py --source data/
   ```

   This command builds a FAISS index (`index/faiss_index.pkl`).

3. **Start the interactive chatbot**:

   ```bash
   python -m llamaindex_rag_chatbot.chat
   ```

   You will be prompted for a query; the bot will return an answer with citations.

---

## Project Structure

```
llamaindex-rag-chatbot/
├─ data/                 # Sample documents used for indexing
├─ index/                # Persisted vector store files (FAISS, Chroma, …)
├─ llamaindex_rag_chatbot/
│   ├─ __init__.py
│   ├─ config.py         # Centralised configuration handling (env vars, defaults)
│   ├─ ingestion.py      # Document loaders & chunking utilities
│   ├─ indexing.py       # Index creation & persistence logic
│   ├─ retrieval.py      # Retrieval wrapper around LlamaIndex
│   ├─ generation.py     # LLM wrapper (prompt templates, streaming)
│   └─ chat.py           # CLI entry‑point for interactive sessions
├─ scripts/
│   ├─ index_documents.py   # Convenience script to build the index
│   └─ run_server.py        # Starts FastAPI UI (optional)
├─ tests/                # Unit & integration tests
├─ .env.example          # Template for environment variables
├─ requirements.txt
├─ pyproject.toml        # Poetry configuration (if used)
└─ README.md             # <-- This file
```

---

## Configuration

All configurable options live in `llamaindex_rag_chatbot/config.py` and can be overridden via environment variables or a `.env` file.

| Variable | Description | Default |
|----------|-------------|---------|
| `LLM_PROVIDER` | Which LLM backend to use (`openai`, `anthropic`, `cohere`, …) | `openai` |
| `EMBEDDING_MODEL` | Embedding model name (e.g., `text-embedding-ada-002`) | `text-embedding-ada-002` |
| `VECTOR_STORE` | Vector store implementation (`faiss`, `chroma`, `pinecone`) | `faiss` |
| `TOP_K` | Number of retrieved chunks per query | `5` |
| `MAX_TOKENS` | Max tokens for LLM completion | `512` |
| `TEMPERATURE` | Sampling temperature for LLM | `0.2` |

---

## Usage

### Indexing Documents

The `scripts/index_documents.py` utility accepts a directory of files and creates a persistent vector store:

```bash
python scripts/index_documents.py \
    --source ./data \
    --store faiss \
    --output ./index/faiss_index.pkl
```

Supported file types are automatically detected via LlamaIndex's built‑in loaders. You can also provide a list of URLs with the `--url` flag.

### Chatting with the Bot

The CLI (`llamaindex_rag_chatbot.chat`) is a thin wrapper around the retrieval‑generation pipeline:

```python
from llamaindex_rag_chatbot import ChatBot

bot = ChatBot()
response = bot.ask("What are the main benefits of Retrieval‑Augmented Generation?")
print(response.answer)
print("Citations:", response.citations)
```

If you prefer a web UI, start the FastAPI server:

```bash
python scripts/run_server.py
```

Then open `http://localhost:8000/docs` for the interactive Swagger UI or `http://localhost:8000` for a minimal HTML chat client.

---

## Testing

Run the test suite with:

```bash
pytest -vv
```

The repository includes:
- Unit tests for each module (`tests/unit/`).
- Integration tests that spin up an in‑memory FAISS store and mock LLM calls (`tests/integration/`).

---

## Contributing

Contributions are welcome! Please follow these steps:

1. **Fork the repository** and clone your fork.
2. Create a **feature branch** (`git checkout -b feature/awesome‑feature`).
3. Install development dependencies:
   ```bash
   pip install -r requirements-dev.txt
   ```
4. Ensure code style with **ruff** and **black**:
   ```bash
   ruff check . && black .
   ```
5. Add tests covering your changes and run the full suite.
6. Submit a **pull request** with a clear description of the change.

See `CONTRIBUTING.md` for detailed guidelines, code of conduct, and release process.

---

## License

This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.
