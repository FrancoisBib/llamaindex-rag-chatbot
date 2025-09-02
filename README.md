# LlamaIndex RAG Chatbot

**LlamaIndex RAG Chatbot** is a reference implementation of a Retrieval‑Augmented Generation (RAG) powered conversational agent built on top of **[LlamaIndex](https://github.com/run-llama/llama_index)**.  The bot indexes a document collection, retrieves relevant passages at query time, and feeds them to a large language model (LLM) to produce grounded, up‑to‑date answers.

---

## Table of Contents

- [Features](#features)
- [Architecture Overview](#architecture-overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Chatbot](#running-the-chatbot)
- [Usage Examples](#usage-examples)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgements](#acknowledgements)

---

## Features

- **RAG pipeline**: Combine dense vector retrieval (via `FAISS` / `Chroma` / `Pinecone`) with LLM generation.
- **Modular index loaders**: Supports plain text, PDF, Markdown, HTML, and custom loaders.
- **Prompt engineering**: Pre‑defined system prompts and optional custom prompt templates.
- **Streaming responses**: Real‑time token streaming for a smoother UI experience.
- **Docker support**: Official Dockerfile for reproducible environments.
- **Extensible**: Plug‑in new LLM providers, retrievers, or storage back‑ends with minimal code changes.

---

## Architecture Overview

```mermaid
flowchart TD
    subgraph Indexing
        Docs[Document Corpus] -->|Loaders| Loader[LLamaIndex Loaders]
        Loader -->|Chunk & Embed| Index[Vector Store (FAISS/Chroma)]
    end
    subgraph Retrieval
        Query[User Query] -->|Retriever| Index
        Index -->|Top‑k passages| Retrieved[Relevant Passages]
    end
    subgraph Generation
        Retrieved -->|Prompt Template| Prompt[Prompt Builder]
        Prompt -->|LLM| LLM[OpenAI / Anthropic / Llama]
        LLM -->|Answer| Response[Chat UI / CLI]
    end
```

1. **Document ingestion** – Documents are loaded, split into chunks, embedded, and stored in a vector store.
2. **Retrieval** – At query time the retriever fetches the most relevant chunks.
3. **Generation** – Retrieved chunks are injected into a system prompt and sent to the LLM, which produces a grounded answer.

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Python      | >=3.9   |
| pip         | latest  |
| OpenAI API key (or compatible LLM endpoint) |
| Optional: Docker & Docker‑Compose |

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot
```

### 2. Create a virtual environment (recommended)

```bash
python -m venv .venv
source .venv/bin/activate   # On Windows: .venv\Scripts\activate
```

### 3. Install Python dependencies

```bash
pip install -r requirements.txt
```

### 4. (Optional) Install with Docker

```bash
docker compose up --build
```

---

## Configuration

Configuration is handled via a **`.env`** file at the project root.  Example:

```dotenv
# .env
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx
LLM_MODEL=gpt-4o-mini   # or "claude-3-5-sonnet-20240620"
VECTOR_STORE=faiss      # options: faiss, chroma, pinecone
INDEX_PATH=./data/index.faiss
CHUNK_SIZE=1024
CHUNK_OVERLAP=200
TOP_K=5
```

> **Tip** – Use `dotenv` to load the file automatically (`python -m dotenv run -- python app.py`).

---

## Running the Chatbot

### CLI mode

```bash
python -m llamaindex_rag_chatbot.cli
```

You will be presented with an interactive prompt.  Type `exit` or press `Ctrl‑D` to quit.

### Web UI (FastAPI + React)

```bash
uvicorn llamaindex_rag_chatbot.api:app --host 0.0.0.0 --port 8000
```

Open `http://localhost:8000` in a browser.  The UI uses Server‑Sent Events (SSE) to stream LLM tokens.

---

## Usage Examples

### 1. Index a custom document folder

```bash
python -m llamaindex_rag_chatbot.index \
    --source ./my_documents \
    --store faiss \
    --output ./data/my_index.faiss
```

### 2. Ask a question via the CLI

```bash
>>> How does retrieval‑augmented generation improve answer factuality?
[Streaming answer ...]
```

### 3. Call the REST endpoint (cURL example)

```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"query": "Explain the difference between dense and sparse retrieval"}'
```

---

## Testing

The repository includes a small test suite based on **pytest**.

```bash
pip install -r dev-requirements.txt
pytest -vv
```

Tests cover:
- Document loading & chunking
- Vector store indexing & retrieval
- Prompt construction
- End‑to‑end RAG pipeline (mocked LLM)

---

## Contributing

Contributions are welcome!  Follow these steps:

1. **Fork** the repository and create a new branch.
2. Ensure code follows the existing style (`black`, `isort`, `flake8`).
3. Add or update tests for new functionality.
4. Run the full test suite (`pytest`).
5. Submit a Pull Request with a clear description of the change.

See `CONTRIBUTING.md` for detailed guidelines on development workflow, CI checks, and code of conduct.

---

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## Acknowledgements

- **LlamaIndex** – the core library that powers data ingestion, indexing, and retrieval.
- **OpenAI / Anthropic** – for providing the LLM endpoints used in the examples.
- **FAISS / Chroma** – vector store back‑ends used for similarity search.
- Community contributors who helped improve the RAG pipeline and documentation.

---

---

*Happy building!*
