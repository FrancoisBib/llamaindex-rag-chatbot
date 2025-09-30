# LlamaIndex RAG Chatbot

## Overview

**LlamaIndex RAG Chatbot** is a reference implementation that demonstrates how to build a Retrieval‑Augmented Generation (RAG) chatbot using the **LlamaIndex** library. The bot retrieves relevant documents from a vector store, augments the prompt with the retrieved context, and generates answers with a large language model (LLM).

The repository showcases:
- Integration of LlamaIndex data loaders, indexes, and query engines.
- A simple FastAPI/WebSocket interface for interactive chat.
- Configuration options for different LLM providers, embeddings, and storage back‑ends.

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Chatbot](#running-the-chatbot)
- [Usage Example](#usage-example)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)

## Features

- **RAG pipeline** built on LlamaIndex (data ingestion → vector index → query engine).
- Support for multiple LLMs (OpenAI, Anthropic, Cohere, etc.) and embedding models.
- Pluggable vector stores (FAISS, Pinecone, Weaviate, etc.).
- Async FastAPI backend with optional WebSocket UI.
- Dockerfile for containerised deployment.
- Comprehensive type hints and unit tests.

## Prerequisites

| Requirement | Minimum version |
|-------------|-----------------|
| Python      | 3.9             |
| pip         | latest          |
| Docker (optional) | 20.10+ |

You will also need API keys for the LLM and embedding services you intend to use (e.g., `OPENAI_API_KEY`).

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

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. (Optional) Install the package in editable mode

```bash
pip install -e .
```

## Configuration

Configuration is handled via environment variables or a `.env` file at the project root. The most common settings are:

| Variable | Description | Example |
|----------|-------------|---------|
| `LLM_PROVIDER` | LLM backend (`openai`, `anthropic`, `cohere`, …) | `openai` |
| `LLM_MODEL` | Model name for the selected provider | `gpt-4o-mini` |
| `EMBEDDING_MODEL` | Embedding model name | `text-embedding-3-large` |
| `VECTOR_STORE` | Vector store implementation (`faiss`, `pinecone`, `weaviate`) | `faiss` |
| `DATA_PATH` | Path to the directory containing source documents | `data/` |
| `API_KEY` | API key for the chosen LLM/embedding service | `sk-...` |

Create a `.env` file:

```dotenv
LLM_PROVIDER=openai
LLM_MODEL=gpt-4o-mini
EMBEDDING_MODEL=text-embedding-3-large
VECTOR_STORE=faiss
DATA_PATH=./data
OPENAI_API_KEY=sk-...
```

The project uses **python‑dotenv** to load these variables automatically.

## Running the Chatbot

### Local development (FastAPI server)

```bash
uvicorn llamaindex_rag_chatbot.main:app --reload
```

The API will be available at `http://127.0.0.1:8000`. Swagger UI can be accessed at `/docs`.

### Docker deployment

```bash
# Build the image
docker build -t llamaindex-rag-chatbot .

# Run the container (make sure to pass your .env file)
docker run -p 8000:8000 --env-file .env llamaindex-rag-chatbot
```

## Usage Example

### API endpoint

`POST /chat`

**Request body**
```json
{
  "message": "Explain the concept of vector similarity search."
}
```

**Response**
```json
{
  "answer": "Vector similarity search…",
  "sources": ["doc1.pdf", "doc2.txt"]
}
```

### Python client (optional helper)

```python
import httpx

url = "http://127.0.0.1:8000/chat"
payload = {"message": "What is Retrieval‑Augmented Generation?"}

resp = httpx.post(url, json=payload)
print(resp.json()["answer"])
```

## Testing

The repository includes a pytest suite. To run tests:

```bash
pytest -v
```

Coverage is measured with `coverage.py` and can be displayed via:

```bash
coverage run -m pytest && coverage report -m
```

## Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository.
2. Create a **feature branch** (`git checkout -b feat/your-feature`).
3. Write code adhering to the existing style (type hints, docstrings, linting with `ruff`).
4. Add or update tests.
5. Ensure all tests pass and coverage does not decrease.
6. Open a **Pull Request** with a clear description of the change.

### Development checklist
- [ ] Run `ruff check .` and fix any linting issues.
- [ ] Update the documentation (README, docstrings) if you add new functionality.
- [ ] Verify the Docker image builds successfully.

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

*Happy hacking with LlamaIndex and RAG!*