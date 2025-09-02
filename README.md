# LlamaIndex RAG Chatbot

## Overview

**LlamaIndex RAG Chatbot** is a reference implementation that demonstrates how to build a Retrieval‑Augmented Generation (RAG) powered chatbot using the **LlamaIndex** library. The project showcases:
- Indexing of arbitrary document collections (PDF, Markdown, plain text, etc.)
- Efficient vector‑store retrieval using popular back‑ends (FAISS, Chroma, Pinecone, etc.)
- Integration with LLMs (OpenAI, Anthropic, Llama‑2, etc.) to generate context‑aware responses
- A simple FastAPI / Streamlit front‑end for interactive chatting
- A clear structure that is easy to extend, test, and contribute to.

The README below provides everything a developer needs to get started, run the demo, and contribute.

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Chatbot](#running-the-chatbot)
- [Project Structure](#project-structure)
- [Key Concepts](#key-concepts)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)

---

## Prerequisites

| Requirement | Recommended Version |
|-------------|----------------------|
| Python      | 3.9 – 3.12           |
| pip         | >=23.0               |
| Git         | any recent version  |

You will also need API keys for the LLM and (optionally) the vector store you choose. See the **Configuration** section for details.

---

## Installation

```bash
# Clone the repository
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate   # on Windows use `.venv\Scripts\activate`

# Install the core dependencies
pip install -r requirements.txt
```

The `requirements.txt` includes:
- `llama-index` (core LlamaIndex library)
- `openai`, `anthropic`, `together` (LLM providers – optional)
- `faiss-cpu` / `chromadb` (vector‑store back‑ends)
- `fastapi`, `uvicorn`, `streamlit` (API & UI)
- `pytest`, `pytest‑cov` (testing)

---

## Configuration

Configuration is handled through environment variables or a `.env` file at the project root. The project uses **python‑dotenv** to load variables automatically.

| Variable | Description | Example |
|----------|-------------|---------|
| `LLM_PROVIDER` | The LLM provider to use (`openai`, `anthropic`, `together`, `llama_cpp`). | `openai` |
| `OPENAI_API_KEY` | Your OpenAI API key (required if `LLM_PROVIDER=openai`). | `sk-...` |
| `ANTHROPIC_API_KEY` | Anthropic API key (if using Anthropic). | `sk-ant-...` |
| `VECTOR_STORE` | Vector store backend (`faiss`, `chromadb`, `pinecone`). | `faiss` |
| `FAISS_INDEX_PATH` | Path where the FAISS index will be persisted. | `./data/faiss_index` |
| `DOCS_PATH` | Directory containing source documents to be indexed. | `./data/docs` |
| `CHUNK_SIZE` | Number of tokens per chunk (default = 512). | `512` |
| `CHUNK_OVERLAP` | Overlap between chunks (default = 50). | `50` |

Create a `.env.example` file for reference and copy it to `.env` before running the app.

---

## Running the Chatbot

### 1. Index your documents

```bash
python scripts/build_index.py
```

The script reads all files under `DOCS_PATH`, splits them into chunks, creates embeddings using the selected LLM provider, and stores them in the configured vector store.

### 2. Start the API server

```bash
uvicorn app.main:app --reload
```

The FastAPI server will be available at `http://127.0.0.1:8000`. Swagger UI can be accessed at `/docs`.

### 3. Launch the UI (optional)

```bash
streamlit run ui/chat_interface.py
```

A Streamlit UI will open in your browser, allowing you to interact with the chatbot without writing any client code.

---

## Project Structure

```
llamaindex-rag-chatbot/
│
├─ app/                     # FastAPI application
│   ├─ __init__.py
│   ├─ main.py              # API routes & startup logic
│   └─ rag_service.py       # Core RAG pipeline (retrieval + generation)
│
├─ ui/                      # Streamlit front‑end
│   └─ chat_interface.py
│
├─ scripts/                 # Utility scripts (index building, data cleaning)
│   └─ build_index.py
│
├─ data/                    # Sample documents & persisted indexes
│   └─ docs/                # Place your source files here
│
├─ tests/                   # Unit and integration tests
│   └─ test_rag_service.py
│
├─ .env.example             # Example environment configuration
├─ requirements.txt
├─ pyproject.toml           # Optional – for poetry users
└─ README.md                # ← you are here
```

---

## Key Concepts

### Retrieval‑Augmented Generation (RAG)
1. **Retrieval** – The system first queries a vector store to fetch the most relevant document chunks for a user query.
2. **Augmentation** – Retrieved chunks are concatenated with the original prompt and sent to the LLM.
3. **Generation** – The LLM produces a response that is grounded in the retrieved knowledge, reducing hallucinations.

### LlamaIndex
- Provides a **unified API** for data loaders, text splitters, embedding models, and vector stores.
- Supports **Composable Graphs**, allowing you to chain multiple retrieval strategies (e.g., hybrid BM25 + vector search).
- Offers **Query Engines** that encapsulate the full RAG flow, which we expose via `rag_service.py`.

---

## Testing

Run the test suite with coverage reporting:

```bash
pytest --cov=app tests/
```

The tests cover:
- Document loading & chunking
- Vector‑store indexing & similarity search
- End‑to‑end RAG pipeline (mocked LLM responses)
- API endpoint validation

---

## Contributing

We welcome contributions! Please follow these steps:
1. **Fork** the repository and clone your fork.
2. Create a feature branch: `git checkout -b feat/your-feature`.
3. Ensure code style with `ruff` (or `flake8`) and type checking with `mypy`.
4. Add or update tests for new functionality.
5. Run the full test suite (`pytest`).
6. Open a Pull Request with a clear description of the change.

### Development Guidelines
- Keep functions small and single‑purpose.
- Document public functions with docstrings that include type hints.
- Use `logging` instead of `print` for runtime information.
- Update the `CHANGELOG.md` (if present) with a concise entry for each PR.

---

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## Acknowledgements

- **LlamaIndex** – the backbone of the retrieval layer.
- **OpenAI / Anthropic / Together** – for providing powerful LLM APIs.
- Community contributors who help improve the example and documentation.

---

*Happy hacking!*