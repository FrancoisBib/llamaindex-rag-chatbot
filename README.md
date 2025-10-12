# LlamaIndex RAG Chatbot

**LlamaIndex RAG Chatbot** is a reference implementation of a Retrieval‑Augmented Generation (RAG) chatbot built on top of the **LlamaIndex** ecosystem. It demonstrates how to combine vector stores, document loaders, and LLMs to build a conversational agent that can answer questions using both its internal knowledge and external data sources.

---

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Chatbot](#running-the-chatbot)
- [Project Structure](#project-structure)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- **Retrieval‑Augmented Generation** using LlamaIndex's `VectorStoreIndex`.
- Supports multiple document loaders (PDF, Markdown, plain text, etc.).
- Plug‑and‑play LLM back‑ends (OpenAI, Anthropic, Llama.cpp, etc.).
- Simple CLI and optional FastAPI web UI.
- Extensible architecture for custom retrievers, query engines, and post‑processors.

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Python      | 3.9 or newer |
| pip         | latest |
| Git         | any |
| An LLM API key (OpenAI, Anthropic, etc.) | – |

> **Note**: The project uses `poetry` for dependency management, but a `requirements.txt` is also provided for pip users.

---

## Installation

### Using Poetry (recommended)

```bash
# Clone the repository
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Install dependencies
poetry install

# Activate the virtual environment
poetry shell
```

### Using pip

```bash
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot
pip install -r requirements.txt
```

---

## Configuration

All configurable options are stored in `config.yaml`. A minimal example:

```yaml
# config.yaml
llm:
  provider: openai            # or anthropic, llama_cpp, etc.
  model: gpt-4o-mini
  api_key: "${OPENAI_API_KEY}"  # can be set via environment variable

index:
  type: vector_store
  store: chromadb
  persist_dir: ./data/chroma

loader:
  - type: directory
    path: ./data/documents
    extensions: [".pdf", ".md", ".txt"]
```

You can override any setting at runtime via command‑line flags (see `python -m chatbot --help`).

---

## Running the Chatbot

### CLI mode

```bash
python -m chatbot --config config.yaml
```

You will be dropped into an interactive prompt where you can type natural‑language questions.

### Web UI (FastAPI)

```bash
uvicorn app.main:app --reload
```

Open your browser at `http://127.0.0.1:8000/docs` to explore the OpenAPI UI or navigate to `http://127.0.0.1:8000` for a simple chat interface.

---

## Project Structure

```
llamaindex-rag-chatbot/
├─ app/                     # FastAPI entry point (optional UI)
│   ├─ main.py
│   └─ routes.py
├─ chatbot/                 # Core CLI implementation
│   ├─ __init__.py
│   ├─ cli.py               # argparse wrapper
│   ├─ engine.py            # Retrieval + generation pipeline
│   └─ utils.py
├─ data/                    # Sample documents (git‑ignored by default)
├─ tests/                   # Unit and integration tests
│   └─ test_engine.py
├─ config.yaml              # Default configuration file
├─ requirements.txt         # pip‑compatible dependencies
├─ pyproject.toml           # Poetry project definition
└─ README.md                # ← you are here
```

---

## Testing

The project uses **pytest**. To run the test suite:

```bash
pytest -v
```

Continuous integration is configured via GitHub Actions (see `.github/workflows/ci.yml`).

---

## Contributing

Contributions are welcome! Follow these steps:

1. **Fork** the repository and clone your fork.
2. Create a new branch for your feature or bug‑fix:
   ```bash
   git checkout -b my-feature
   ```
3. Make your changes, ensuring that:
   - Code follows the existing style (black + isort).
   - New functionality is covered by tests.
   - Documentation (README, docstrings) is updated.
4. Run the test suite and linting tools:
   ```bash
   poetry run black . && poetry run isort . && poetry run flake8
   poetry run pytest
   ```
5. Open a Pull Request against the `main` branch.

Please read the full **CONTRIBUTING.md** for detailed guidelines on coding standards, commit messages, and release process.

---

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## Acknowledgments

- **LlamaIndex** – the core library that powers retrieval and indexing.
- **OpenAI**, **Anthropic**, **ChromaDB**, and other ecosystem partners.

---

*Happy building with LlamaIndex!*