# LlamaIndex RAG Chatbot

A **retrieval‑augmented generation (RAG)** chatbot built on top of **[LlamaIndex](https://github.com/run-llama/llama_index)**. The bot can answer user questions by combining the reasoning capabilities of large language models (LLMs) with up‑to‑date information retrieved from a document store.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Architecture Diagram](#architecture-diagram)
4. [Getting Started](#getting-started)
   - [Prerequisites](#prerequisites)
   - [Installation](#installation)
   - [Run the Demo](#run-the-demo)
5. [Usage Guide](#usage-guide)
   - [Command‑line Interface](#command-line-interface)
   - [Python API](#python-api)
6. [Adding Your Own Data](#adding-your-own-data)
7. [Testing](#testing)
8. [Contributing](#contributing)
9. [License](#license)

---

## Project Overview

The **LlamaIndex RAG Chatbot** demonstrates how to:

* Index a collection of unstructured documents (PDF, TXT, Markdown, etc.) with LlamaIndex.
* Store the resulting embeddings in a vector store (currently **FAISS**, but the code is abstracted to support any `BaseVectorStore` implementation).
* Retrieve the most relevant chunks at query time.
* Pass the retrieved context to an LLM (OpenAI, Anthropic, Llama‑2, etc.) to generate a grounded answer.
* Serve the pipeline through a simple FastAPI web service or a CLI.

All heavy lifting (tokenisation, chunking, embedding, retrieval) is handled by LlamaIndex, allowing developers to focus on the chatbot logic.

---

## Features

| Feature | Description |
|---|---|
| **Modular architecture** | Separate components for ingestion, indexing, retrieval, and generation. Easy to swap vector stores or LLM providers. |
| **Multi‑source ingestion** | Supports PDFs, plain text, Markdown, and custom loaders via LlamaIndex `Document` objects. |
| **Hybrid retrieval** | Combine similarity search with keyword‑based BM25 for higher recall. |
| **Streaming responses** | FastAPI endpoint streams tokens from the LLM for a chat‑like experience. |
| **Config‑driven** | All parameters (model, temperature, chunk size, etc.) are defined in a single `config.yaml`. |
| **Docker ready** | Dockerfile and `docker-compose.yml` for one‑click local deployment. |
| **Extensive tests** | Unit tests for each pipeline stage (ingestion, indexing, retrieval, generation). |
| **Contribution friendly** | Linting, type hints, and contribution guidelines included. |

---

## Architecture Diagram

```
+----------------+      +----------------+      +-------------------+
|   Document(s)  | ---> |  LlamaIndex    | ---> | Vector Store (FAISS) |
+----------------+      +----------------+      +-------------------+
                                 ^                     |
                                 |                     v
                         +----------------+   +-------------------+
                         | Retrieval      |   | LLM (OpenAI, …)   |
                         +----------------+   +-------------------+
                                 |                     |
                                 +--------+------------+
                                          |
                                          v
                                   +----------------+
                                   | FastAPI / CLI |
                                   +----------------+
```

---

## Getting Started

### Prerequisites

* **Python ≥ 3.9** (tested with 3.10 & 3.11)
* An OpenAI API key (or another LLM provider key) – see the *Configuration* section.
* `git` and `docker` (optional, for containerised execution).

### Installation

```bash
# Clone the repository
git clone https://github.com/FrancoisBib/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate  # on Windows: .venv\Scripts\activate

# Install the core dependencies
pip install -r requirements.txt
```

#### Optional: Install with Docker

```bash
# Build the image
docker build -t llamaindex-rag-chatbot .

# Run the container (the config file is mounted for easy editing)
docker run -p 8000:8000 -v $(pwd)/config.yaml:/app/config.yaml llamaindex-rag-chatbot
```

### Run the Demo

1. **Create a configuration file** – copy the example and edit your keys:
   ```bash
   cp config.example.yaml config.yaml
   ```
   Set `openai_api_key` (or the appropriate provider key) and adjust `model_name` if needed.

2. **Index sample data** (the repo ships a `data/` folder with a few PDFs):
   ```bash
   python -m rag_chatbot.ingest data/
   ```
   This command parses the documents, creates embeddings, and stores them in `index/faiss_index.pkl`.

3. **Start the API**:
   ```bash
   uvicorn rag_chatbot.api:app --reload
   ```
   Open your browser at `http://127.0.0.1:8000/docs` to explore the OpenAPI UI.

4. **Try the CLI** (alternative to the web UI):
   ```bash
   python -m rag_chatbot.cli "What is the capital of France?"
   ```

---

## Usage Guide

### Command‑line Interface

The CLI is a thin wrapper around the same retrieval‑generation pipeline used by the API.

```bash
python -m rag_chatbot.cli "<your question>"
```

You can also start an interactive session:

```bash
python -m rag_chatbot.cli
# Then type questions line‑by‑line, press Ctrl‑C to exit.
```

### Python API

If you want to embed the chatbot in another application, import the high‑level `Chatbot` class:

```python
from rag_chatbot.core import Chatbot

# Initialise – the class reads config.yaml automatically
bot = Chatbot()

# Ask a question
answer = bot.ask("Explain the difference between supervised and unsupervised learning.")
print(answer)
```

All components are exposed if you need fine‑grained control:

```python
from rag_chatbot.ingest import DocumentIngestor
from rag_chatbot.retriever import Retriever
from rag_chatbot.generator import LLMGenerator
```

---

## Adding Your Own Data

1. **Place documents** in a folder (e.g., `my_data/`). Supported formats: `.pdf`, `.txt`, `.md`, `.docx`.
2. **Run the ingestion script**:
   ```bash
   python -m rag_chatbot.ingest my_data/
   ```
   The script automatically:
   * Loads each file via LlamaIndex loaders.
   * Splits text into chunks (default 512 tokens, configurable).
   * Generates embeddings using the model defined in `config.yaml`.
   * Persists the vector store.
3. **Re‑start the API or CLI** – the new knowledge is now searchable.

> **Tip:** For large corpora, consider using a persistent vector database such as **Chroma**, **Pinecone**, or **Weaviate**. The `Retriever` class accepts any `BaseVectorStore` implementation; just change the `vector_store` entry in `config.yaml`.

---

## Testing

The repository includes a test suite based on **pytest**.

```bash
pip install -r dev-requirements.txt
pytest -q
```

Key test modules:
* `tests/test_ingest.py` – verifies document loading and indexing.
* `tests/test_retriever.py` – checks similarity search returns expected IDs.
* `tests/test_generator.py` – mocks LLM calls to ensure prompt formatting.
* `tests/test_api.py` – integration tests for the FastAPI endpoints.

---

## Contributing

Contributions are welcome! Please follow these steps:

1. **Fork the repository** and create a feature branch.
2. **Install development dependencies**:
   ```bash
   pip install -r dev-requirements.txt
   pre-commit install   # sets up linting & formatting hooks
   ```
3. **Write tests** for any new functionality.
4. **Run the full test suite** before pushing:
   ```bash
   pytest && flake8 && mypy rag_chatbot
   ```
5. **Open a Pull Request** with a clear description of the change.

### Code Style

* Use **PEP 8** formatting (auto‑fixed by `black`).
* Type‑annotate public functions (`mypy --strict`).
* Keep imports sorted (`isort`).

---

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## Acknowledgements

* **LlamaIndex** – the backbone for document ingestion, indexing, and retrieval.
* **OpenAI**, **Anthropic**, **Meta Llama‑2** – for the LLM back‑ends.
* The open‑source community for the vector‑store implementations used in the examples.

---

*Happy coding!*
