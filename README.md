# LlamaIndex RAG Chatbot

![Python](https://img.shields.io/badge/python-3.9%2B-blue) ![License](https://img.shields.io/badge/license-MIT-green)

A **Retrieval‑Augmented Generation (RAG)** chatbot built on top of **[LlamaIndex](https://github.com/run-llama/llama_index)**. The bot combines a large language model (LLM) with a vector store to retrieve relevant context from your own documents, enabling accurate and up‑to‑date answers.

---

## Table of Contents

- [Features](#features)
- [Demo](#demo)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Configuration](#configuration)
  - [Running the Bot](#running-the-bot)
- [Project Structure](#project-structure)
- [Usage Examples](#usage-examples)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgements](#acknowledgements)

---

## Features

- **RAG pipeline**: Retrieve relevant chunks from a local or remote document store and feed them to the LLM.
- **Modular indexers**: Supports `SimpleDirectoryReader`, `PDFReader`, `CSVReader`, and custom loaders.
- **Multiple vector stores**: Out‑of‑the‑box support for **FAISS**, **Chroma**, **Pinecone**, and **Weaviate**.
- **Chat history management**: Maintains conversation context across turns.
- **Extensible**: Plug‑in new LLM providers (OpenAI, Azure OpenAI, Ollama, etc.) and custom prompts.
- **Docker ready**: One‑click containerisation for reproducible environments.

---

## Demo

> **Note**: A live demo is hosted at https://demo.llamaindex-rag-chatbot.example.com (replace with actual URL if available).

![Chatbot Screenshot](docs/screenshot.png)

---

## Getting Started

### Prerequisites

- Python **3.9** or newer.
- An LLM API key (OpenAI, Azure OpenAI, Anthropic, etc.).
- Optional: A vector‑store service account (e.g., Pinecone) if you prefer a hosted store.

### Installation

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

### Configuration

Create a ```.env``` file at the project root and populate the required variables:

```dotenv
# LLM configuration (choose one)
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx
# or Azure OpenAI
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com/
AZURE_OPENAI_API_KEY=xxxxxxxxxxxxxxxxxxxx

# Vector store configuration (optional – defaults to FAISS on disk)
FAISS_INDEX_PATH=./faiss_index
# For Pinecone
PINECONE_API_KEY=xxxxxxxxxxxxxxxxxxxx
PINECONE_ENV=us-west1-gcp
```

> **Tip**: Keep your ```.env``` file out of version control (`git add .gitignore`).

### Running the Bot

#### 1️⃣ Index your documents

```bash
python scripts/build_index.py \
    --source-dir ./data \
    --index-type faiss   # or pinecone, chroma, weaviate
```

The command reads all supported files in ``./data`` and creates a vector index at the location defined in ``FAISS_INDEX_PATH``.

#### 2️⃣ Launch the chatbot UI

```bash
python app.py
```

Open your browser at **http://localhost:8000**. You can now ask questions; the system will retrieve the most relevant passages and generate answers using the configured LLM.

---

## Project Structure

```
llamaindex-rag-chatbot/
│
├─ app.py                     # FastAPI + Streamlit (or Gradio) entry point
├─ requirements.txt           # Python dependencies
├─ .env.example               # Template for environment variables
├─ scripts/
│   └─ build_index.py         # Utility to create / update the vector index
├─ llama_index/
│   ├─ __init__.py
│   ├─ loaders.py             # Custom document loaders
│   └─ rag_pipeline.py        # Core RAG logic (retriever + generator)
├─ data/                      # Sample documents (Markdown, PDF, CSV …)
├─ tests/                     # Unit and integration tests
└─ docs/                      # Additional documentation, screenshots
```

---

## Usage Examples

### Simple Python API

```python
from llama_index.rag_pipeline import RAGChatbot

bot = RAGChatbot(
    llm_model="gpt-4o-mini",
    vector_store="faiss",
    index_path="./faiss_index",
)

response = bot.ask("How does the RAG architecture improve answer factuality?")
print(response)
```

### Streamlit UI (default)

Run ``python app.py`` and interact via the web UI. The UI displays:

- User query
- Retrieved document snippets
- LLM‑generated answer
- Token usage statistics

---

## Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository and create a feature branch.
2. Ensure code style with **ruff** and type checking with **mypy**:
   ```bash
   pip install ruff mypy
   ruff check .
   mypy .
   ```
3. Add or update tests in the ``tests/`` folder. Run the test suite:
   ```bash
   pytest -q
   ```
4. Submit a **Pull Request** with a clear description of the change.

See ``CONTRIBUTING.md`` for detailed guidelines.

---

## License

This project is licensed under the **MIT License** – see the ``LICENSE`` file for details.

---

## Acknowledgements

- **LlamaIndex** – the underlying framework that makes data‑aware LLM applications simple.
- **OpenAI**, **Azure OpenAI**, **Anthropic**, **Ollama** – for providing the large language models.
- The open‑source community for the vector‑store integrations (FAISS, Chroma, Pinecone, Weaviate).

---

*Happy building with LlamaIndex!*