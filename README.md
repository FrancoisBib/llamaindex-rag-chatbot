# LlamaIndex RAG Chatbot

## Overview

**LlamaIndex RAG Chatbot** is a reference implementation that demonstrates how to build a Retrieval‑Augmented Generation (RAG) powered conversational assistant using the **LlamaIndex** framework. The bot combines a vector store for document retrieval with a large language model (LLM) to generate context‑aware responses.

- **RAG** – Retrieve relevant chunks from a knowledge base and augment the LLM prompt with this information.
- **LlamaIndex** – Provides a simple, modular API to create indexes, query them, and integrate with various LLM providers.
- **Chat‑style UI** – A minimal web interface (Streamlit) that lets you interact with the bot in real time.

The repository is intended for developers who want to:
- Learn the best practices for wiring LlamaIndex with vector stores and LLMs.
- Quickly prototype a knowledge‑base‑backed chatbot.
- Contribute enhancements such as new data loaders, custom retrievers, or UI features.

---

## Table of Contents

1. [Features](#features)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Running the Chatbot](#running-the-chatbot)
6. [Project Structure](#project-structure)
7. [Usage Example](#usage-example)
8. [Testing](#testing)
9. [Contributing](#contributing)
10. [License](#license)

---

## Features

- **Modular index creation** – Supports CSV, PDF, Markdown, and plain‑text loaders.
- **Pluggable vector stores** – Default: `FAISS`; can be swapped for `Pinecone`, `Weaviate`, `Qdrant`, etc.
- **LLM agnostic** – Works with OpenAI, Anthropic, Cohere, HuggingFace, and any compatible endpoint.
- **Streaming responses** – Real‑time token streaming for a smooth chat experience.
- **Docker support** – Ready-to‑run container for easy deployment.
- **Extensible** – Add custom retrievers, query transforms, or post‑processing pipelines.

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Python      | >=3.9   |
| pip         | latest  |
| Git         | any     |
| (Optional) Docker | >=20.10 |

You also need API keys for the LLM provider you intend to use (e.g., `OPENAI_API_KEY`).

---

## Installation

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

### Docker (alternative)

```bash
docker build -t llamaindex-rag-chatbot .
# Run the container, passing required env vars (see Configuration section)
 docker run -p 8501:8501 \
    -e OPENAI_API_KEY=$OPENAI_API_KEY \
    llamaindex-rag-chatbot
```

---

## Configuration

The bot reads its configuration from environment variables or a `.env` file at the project root. Example `.env.example`:

```dotenv
# LLM configuration
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx
LLM_MODEL=gpt-4o-mini   # or any model name supported by the provider
TEMPERATURE=0.7

# Vector store configuration (FAISS uses local storage)
FAISS_INDEX_PATH=./data/faiss_index

# Data source directory (documents to index)
DATA_DIR=./data/documents

# Streamlit UI settings
STREAMLIT_PORT=8501
```

Copy the file to `.env` and adjust values as needed.

---

## Running the Chatbot

### 1. Build the index (one‑time step)

```bash
python scripts/build_index.py
```

The script walks through `DATA_DIR`, loads supported file types, creates embeddings using the configured LLM, and persists the vector store.

### 2. Start the chat UI

```bash
streamlit run app/chat_interface.py --server.port $STREAMLIT_PORT
```

Open `http://localhost:8501` in your browser. Type a question and the bot will retrieve relevant passages and generate a response.

---

## Project Structure

```
llamaindex-rag-chatbot/
├─ app/                     # Streamlit UI components
│   └─ chat_interface.py    # Main chat page
├─ data/                    # Default data folder (documents & FAISS index)
├─ scripts/                 # Utility scripts (index building, evaluation)
│   └─ build_index.py       # Index creation script
├─ llama_index/             # Core LlamaIndex wrappers (retriever, query engine)
│   ├─ __init__.py
│   └─ rag_engine.py        # High‑level RAG pipeline
├─ tests/                   # Unit and integration tests
│   └─ test_rag_engine.py
├─ .env.example             # Example environment configuration
├─ requirements.txt         # Python dependencies
├─ Dockerfile               # Container definition
└─ README.md                # ← you are here
```

---

## Usage Example

```python
from llama_index.rag_engine import RagChatEngine
import os

# Load configuration from env
engine = RagChatEngine(
    llm_model=os.getenv("LLM_MODEL"),
    temperature=float(os.getenv("TEMPERATURE", "0.7")),
    vector_store_path=os.getenv("FAISS_INDEX_PATH"),
)

while True:
    query = input("You: ")
    if query.lower() in {"exit", "quit"}:
        break
    response = engine.chat(query)
    print(f"Bot: {response}\n")
```

The `RagChatEngine` encapsulates:
1. **Retriever** – fetches top‑k relevant chunks.
2. **Prompt builder** – injects retrieved text into a system prompt.
3. **LLM call** – streams the answer back to the caller.

---

## Testing

```bash
# Run the test suite
pytest -q
```

The test suite covers:
- Index creation integrity.
- Retrieval relevance (basic sanity checks).
- End‑to‑end chat flow with a mock LLM.

---

## Contributing

Contributions are welcome! Follow these steps:

1. **Fork** the repository.
2. **Create a feature branch** (`git checkout -b feat/your-feature`).
3. **Write tests** for new functionality.
4. **Run the full test suite** (`pytest`).
5. **Submit a Pull Request** with a clear description of the change.

Please adhere to the existing code style (Black, isort) and update the documentation when adding new public APIs.

---

## License

This project is licensed under the **MIT License** – see the `LICENSE` file for details.

---

## Acknowledgements

- **LlamaIndex** – The underlying framework that simplifies data ingestion, indexing, and retrieval.
- **OpenAI / Anthropic / Cohere** – For providing powerful LLM back‑ends.
- **Streamlit** – For the lightweight UI.

---

---

*Happy hacking!*
