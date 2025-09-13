# LlamaIndex RAG Chatbot

**LlamaIndex RAG Chatbot** is a reference implementation of a Retrieval‑Augmented Generation (RAG) powered conversational agent built with **[LlamaIndex](https://github.com/run-llama/llama_index)**.  It demonstrates how to combine a large language model (LLM) with a vector store to answer user queries over custom documents in a natural, chat‑like interface.

---

## Table of Contents

- [Features](#features)
- [Architecture Overview](#architecture-overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Quick‑Start Guide](#quick-start-guide)
- [Running the Bot](#running-the-bot)
- [Development & Extending the Project](#development--extending-the-project)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgements](#acknowledgements)

---

## Features

- **RAG pipeline** using LlamaIndex `RetrieverQueryEngine`.
- Supports **multiple vector stores** (Chroma, Pinecone, Weaviate, etc.) – just set the appropriate environment variable.
- **Modular design** – data ingestion, indexing, and query handling are cleanly separated.
- **FastAPI** backend exposing a `/chat` endpoint that can be consumed by any UI (e.g., Streamlit, React, CLI).
- **Docker** support for reproducible local development and CI.
- **Extensible** – plug‑in your own LLM, embedding model, or document loaders with minimal code changes.

---

## Architecture Overview

```
+-------------------+        +-------------------+        +-------------------+
|   Document Source |  -->   |   Ingestion Layer |  -->   |   Vector Store    |
| (PDF, TXT, URLs)  |        | (LlamaIndex loader|        | (Chroma, Pinecone |
+-------------------+        |   + embedder)    |        |   etc.)           |
                               +-------------------+        +-------------------+
                                        |
                                        v
                               +-------------------+
                               |   Query Engine    |
                               | (Retriever + LLM) |
                               +-------------------+
                                        |
                                        v
                               +-------------------+
                               |   FastAPI Server  |
                               +-------------------+
```

1. **Ingestion** – Documents are loaded using LlamaIndex loaders (e.g., `PDFReader`, `SimpleDirectoryReader`).
2. **Embedding** – Text chunks are embedded with an embedding model (OpenAI `text-embedding-ada-002` by default) and stored in the configured vector store.
3. **Retrieval** – At query time, the `Retriever` fetches the most relevant chunks.
4. **Generation** – The retrieved context is passed to the LLM (OpenAI `gpt-4o` by default) to generate a response.
5. **API** – The FastAPI endpoint returns the generated answer together with the source nodes for transparency.

---

## Prerequisites

| Requirement | Minimum Version |
|-------------|-----------------|
| Python      | 3.9             |
| pip         | 22.0            |
| Docker (optional) | 20.10 |
| An OpenAI API key (or compatible LLM provider) |
| A vector‑store service (Chroma runs locally, others need credentials) |

---

## Installation

```bash
# Clone the repository
git clone https://github.com/FrancoisBib/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate   # on Windows: .venv\Scripts\activate

# Install Python dependencies
pip install -r requirements.txt
```

If you prefer Docker:

```bash
docker build -t llamaindex-rag-chatbot .
```

---

## Configuration

All configuration is driven by environment variables. Create a `.env` file in the project root (or export variables directly) with the following keys:

```dotenv
# OpenAI (or other LLM) configuration
OPENAI_API_KEY=sk-**************
OPENAI_MODEL=gpt-4o               # default model used for generation

# Embedding model (optional – defaults to OpenAI ada)
EMBEDDING_MODEL=text-embedding-ada-002

# Vector store selection – one of: chroma, pinecone, weaviate, qdrant
VECTOR_STORE=chroma

# Pinecone configuration (only needed if VECTOR_STORE=pinecone)
PINECONE_API_KEY=YOUR_PINECONE_KEY
PINECONE_ENVIRONMENT=us-west1-gcp
PINECONE_INDEX_NAME=llamaindex-index

# Chroma configuration (optional – defaults to ./chroma_data)
CHROMA_PERSIST_DIR=./chroma_data

# FastAPI settings
HOST=0.0.0.0
PORT=8000
```

The application reads the `.env` file automatically via `python-dotenv`.

---

## Quick‑Start Guide

### 1️⃣ Ingest Sample Documents

```bash
# Place your .txt/.pdf files inside the `data/` directory.
python scripts/ingest.py --source-dir data/
```

The script will:
- Split documents into chunks (default 500 tokens).
- Compute embeddings.
- Persist the index to the configured vector store.

### 2️⃣ Launch the API Server

```bash
uvicorn app.main:app --host $HOST --port $PORT
```

The server will start at `http://localhost:8000`.

### 3️⃣ Talk to the Bot

You can use `curl`, Postman, or any front‑end. Example with `curl`:

```bash
curl -X POST http://localhost:8000/chat \
     -H "Content-Type: application/json" \
     -d '{"message": "Explain the main benefits of Retrieval‑Augmented Generation."}'
```

The response includes:
- `answer`: the generated text.
- `source_nodes`: a list of retrieved document excerpts (useful for debugging or UI citation).

---

## Running the Bot (Docker)

```bash
# Build (if not already built)
docker build -t llamaindex-rag-chatbot .

# Run – make sure to mount a volume for the vector store and provide the .env file
docker run -p 8000:8000 \
    --env-file .env \
    -v $(pwd)/chroma_data:/app/chroma_data \
    llamaindex-rag-chatbot
```

---

## Development & Extending the Project

### Project Layout

```
llamaindex-rag-chatbot/
│
├─ app/                     # FastAPI application
│   ├─ main.py              # Entry point (router definition)
│   └─ services/            # Query engine wrapper
│
├─ scripts/                 # CLI utilities (ingest, rebuild, evaluate)
│   └─ ingest.py            # Document ingestion script
│
├─ data/                    # Sample documents (git‑ignored)
│
├─ tests/                   # Pytest suite
│
├─ requirements.txt         # Python dependencies
├─ Dockerfile               # Container definition
└─ README.md                # <‑‑ you are here
```

### Adding a New Document Loader

LlamaIndex ships with many loaders (e.g., `HTMLReader`, `YouTubeTranscriptReader`). To add one:
1. Import the loader in `scripts/ingest.py`.
2. Extend the `load_documents` function to handle the new file type.
3. Re‑run the ingestion script.

### Swapping the LLM Provider

Replace the OpenAI client with any LangChain‑compatible LLM (e.g., Anthropic, Cohere) by editing `app/services/query_engine.py`:
```python
from llama_index.llms import OpenAI
# ->
from llama_index.llms import Anthropic

llm = Anthropic(model="claude-3-5-sonnet-20240620")
```
Update the environment variables accordingly (`ANTHROPIC_API_KEY`, etc.).

### Running Tests

```bash
pytest -vv
```
The test suite covers ingestion, retrieval, and API contract.

---

## Contributing

Contributions are welcome! Please follow these steps:
1. **Fork** the repository.
2. Create a **feature branch** (`git checkout -b feat/your-feature`).
3. Ensure code passes linting (`ruff check .`) and tests.
4. Open a **Pull Request** with a clear description of the change.
5. If you add new dependencies, update `requirements.txt` and the Dockerfile.

---

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## Acknowledgements

- **LlamaIndex** – the core library that makes building RAG pipelines straightforward.
- **OpenAI** – for the language and embedding models used in the reference implementation.
- **FastAPI** – for the lightweight, async‑first web framework.
- Community contributors who helped test and improve the example.

---

*Happy hacking with LlamaIndex!*