# LlamaIndex RAG Chatbot

## Overview

The **LlamaIndex RAG Chatbot** is a reference implementation that demonstrates how to build a Retrieval‑Augmented Generation (RAG) powered conversational agent using **LlamaIndex** (formerly *GPT Index*).  It showcases:
- Integration of LlamaIndex with vector stores for fast similarity search.
- A modular pipeline that retrieves relevant documents, augments the prompt, and generates responses with a language model.
- A simple FastAPI web service and a Streamlit UI for interactive chatting.

This repository is intended for developers who want to:
- Learn the best practices for constructing RAG pipelines with LlamaIndex.
- Quickly prototype their own chatbot backed by custom data sources.
- Contribute improvements or new integrations (e.g., additional vector stores, LLM providers, or UI components).

---

## Table of Contents

- [Features](#features)
- [Quick Start](#quick-start)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Running the Demo](#running-the-demo)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Development & Contribution Guidelines](#development--contribution-guidelines)
- [FAQ](#faq)
- [License](#license)

---

## Features

- **Modular RAG pipeline** built with LlamaIndex components (`Document`, `VectorStoreIndex`, `Retriever`, `QueryEngine`).
- **Multiple data loaders** (PDF, TXT, CSV, Markdown) – easy to extend.
- **Pluggable vector stores** – default is `FAISS`, but the code is compatible with any LlamaIndex‑supported store (e.g., `Pinecone`, `Weaviate`, `Qdrant`).
- **LLM agnostic** – works with OpenAI, Anthropic, Llama‑CPP, Azure OpenAI, etc., via the unified LlamaIndex `LLM` interface.
- **FastAPI backend** exposing `/chat` endpoint for programmatic access.
- **Streamlit UI** for a ready‑to‑use web chat interface.
- **Docker support** – one‑click containerised deployment.
- **Extensive type hints & docstrings** for IDE autocompletion.

---

## Quick Start

### Prerequisites

| Requirement | Recommended Version |
|-------------|----------------------|
| Python      | 3.9 – 3.12           |
| pip         | >=23.0               |
| Docker (optional) | >=24.0          |
| OpenAI API key (or another LLM provider) | – |

> **Note**: The project uses the `llama-index` ecosystem. Ensure you have a compatible LLM API key (OpenAI, Anthropic, etc.) set in your environment.

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment (optional but recommended)
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

If you prefer Docker:

```bash
docker build -t llamaindex-rag-chatbot .
```

### Running the Demo

#### 1️⃣ Start the FastAPI backend

```bash
# Export your LLM API key (example for OpenAI)
export OPENAI_API_KEY=sk-...   # Linux/macOS
# set OPENAI_API_KEY=sk-...   # Windows PowerShell

uvicorn app.main:app --reload
```
The API will be available at `http://127.0.0.1:8000`.

#### 2️⃣ Launch the Streamlit UI (in a separate terminal)

```bash
streamlit run ui/chat_interface.py
```
Open the displayed URL (usually `http://localhost:8501`) to start chatting.

#### 3️⃣ Using Docker (all‑in‑one)

```bash
docker run -e OPENAI_API_KEY=sk-... -p 8000:8000 -p 8501:8501 llamaindex-rag-chatbot
```

---

## Project Structure

```
llamaindex-rag-chatbot/
│
├─ app/                     # FastAPI application
│   ├─ main.py              # Entry point, routes definition
│   └─ rag_pipeline.py      # Core RAG logic (index creation, retrieval, generation)
│
├─ data/                    # Sample documents used in the demo
│   ├─ *.pdf, *.txt, *.md
│
├─ ui/                      # Streamlit UI components
│   └─ chat_interface.py    # Interactive chat page
│
├─ tests/                   # Unit & integration tests
│   └─ test_rag_pipeline.py
│
├─ Dockerfile               # Container definition
├─ requirements.txt         # Python dependencies
├─ pyproject.toml           # Optional, for Poetry users
└─ README.md                # <-- you are here!
```

---

## Configuration

Configuration is driven by environment variables and a small `config.yaml` file (optional). The most common settings are:

| Variable | Description | Default |
|----------|-------------|---------|
| `OPENAI_API_KEY` | Your OpenAI secret key (or equivalent for other providers) | – |
| `LLM_MODEL` | Identifier of the LLM to use (e.g., `gpt-3.5-turbo`, `claude-2.1`) | `gpt-3.5-turbo` |
| `VECTOR_STORE` | Vector store backend (`faiss`, `pinecone`, `qdrant`, …) | `faiss` |
| `INDEX_PATH` | Directory where the persisted index is stored | `./index` |
| `DOCS_PATH` | Path to the folder containing source documents | `./data` |

You can create a `.env` file at the project root and load it with `python-dotenv` (already included in requirements). Example:

```dotenv
OPENAI_API_KEY=sk-...
LLM_MODEL=gpt-4o-mini
VECTOR_STORE=faiss
INDEX_PATH=./index
DOCS_PATH=./data
```

---

## Development & Contribution Guidelines

### Setting up a Development Environment

1. Fork the repository and clone your fork.
2. Follow the **Installation** steps above.
3. Install the development extras:
   ```bash
   pip install -r requirements-dev.txt
   ```
4. Run the test suite to ensure everything works:
   ```bash
   pytest -q
   ```

### Code Style

- Follow **PEP 8** and use **ruff** for linting (`ruff check .`).
- Type annotations are required for all public functions.
- Keep the documentation in docstrings and the README up‑to‑date.

### Adding a New Vector Store

1. Implement a thin wrapper that conforms to LlamaIndex's `VectorStore` interface.
2. Add a corresponding entry in `app/rag_pipeline.py` under the `get_vector_store()` factory.
3. Update the `README` section *Configuration* with the new `VECTOR_STORE` option.
4. Write unit tests in `tests/`.

### Submitting a Pull Request

1. Ensure all tests pass (`pytest`).
2. Run `ruff format .` and `ruff check .` – the CI will enforce a clean lint.
3. Provide a clear description of the changes and update the changelog section below.

---

## FAQ

**Q: Can I use a local LLM instead of OpenAI?**
A: Yes. Install the appropriate LlamaIndex LLM wrapper (e.g., `llama-index-llama-cpp`) and set `LLM_MODEL` to the local model identifier. The code automatically picks the correct provider based on the model name.

**Q: How do I persist the index between runs?**
A: The `FAISS` store writes files to the directory defined by `INDEX_PATH`. Ensure the path is mounted as a volume when using Docker.

**Q: My documents are in a remote S3 bucket – can I load them?**
A: Absolutely. Extend `app/rag_pipeline.py` with a custom loader that streams files from S3 (LlamaIndex already provides an `S3Reader`). Then add the loader to the `load_documents()` function.

---

## License

This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.

---

## Changelog

| Version | Date | Description |
|---------|------|-------------|
| 0.1.0 | 2024‑10‑01 | Initial public release – basic FastAPI + Streamlit demo with FAISS & OpenAI. |
| 0.2.0 | 2024‑11‑15 | Added Docker support, environment‑based config, and unit tests. |
| 0.3.0 | 2025‑02‑20 | Refactored to support multiple vector stores and added contribution guide. |

---

---

*Happy coding!*