# LlamaIndex RAG Chatbot

**LlamaIndex RAG Chatbot** is a minimal, production‑ready example that demonstrates how to build a Retrieval‑Augmented Generation (RAG) chatbot using **LlamaIndex** (formerly GPT Index) and **LangChain** compatible LLMs. The bot retrieves relevant documents from a vector store, augments the prompt with the retrieved context, and generates answers with a language model.

---

## Table of Contents

- [Features](#features)
- [Architecture Overview](#architecture-overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Chatbot](#running-the-chatbot)
- [Example Interaction](#example-interaction)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- **RAG pipeline** built on LlamaIndex's `Retriever` and `QueryEngine` abstractions.
- Supports **any LangChain‑compatible LLM** (OpenAI, Azure OpenAI, Anthropic, etc.).
- **Modular vector store** – switch between Pinecone, Weaviate, FAISS, Chroma, etc. with a single config change.
- Simple **CLI** and **FastAPI** interfaces for quick prototyping.
- **Streaming** responses for a better chat experience.
- Docker‑ready for reproducible deployments.

---

## Architecture Overview

```
+-------------------+       +-------------------+       +-------------------+
|   User Input      | ----> |   LlamaIndex      | ----> |   LLM (OpenAI,    |
| (CLI / HTTP)      |       |   Retriever       |       |   Azure, …)       |
+-------------------+       +-------------------+       +-------------------+
                               |   ^
                               |   |
                               v   |
                        +-------------------+
                        | Vector Store      |
                        | (FAISS, Pinecone, |
                        |  Chroma, …)       |
                        +-------------------+
```

1. **Retriever** queries the vector store for the most relevant chunks.
2. Retrieved chunks are concatenated with the user question to form an **augmented prompt**.
3. The LLM generates a response based on this enriched context.

---

## Prerequisites

- **Python ≥ 3.9**
- An **OpenAI API key** (or credentials for another supported LLM provider)
- (Optional) Access to a vector‑store service if you do not want to use the local FAISS fallback.

---

## Installation

```bash
# Clone the repository
git clone https://github.com/your-org/llamaindex-rag-chatbot.git
cd llamaindex-rag-chatbot

# Create a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate  # On Windows use `.venv\Scripts\activate`

# Install dependencies
pip install -r requirements.txt
```

If you prefer Docker:

```bash
docker build -t llamaindex-rag-chatbot .
# Run the container (example with environment variables)
docker run -e OPENAI_API_KEY=$OPENAI_API_KEY -p 8000:8000 llamaindex-rag-chatbot
```

---

## Configuration

Configuration is driven by environment variables or a `.env` file at the project root. The most common settings are:

| Variable | Description | Default |
|----------|-------------|---------|
| `OPENAI_API_KEY` | Your OpenAI API key | – |
| `LLM_MODEL` | Model name for the LLM (e.g., `gpt-3.5-turbo`) | `gpt-3.5-turbo` |
| `VECTOR_STORE` | Which vector store to use (`faiss`, `pinecone`, `chroma`, …) | `faiss` |
| `PINECONE_API_KEY` | Pinecone API key (if using Pinecone) | – |
| `PINECONE_ENV` | Pinecone environment (e.g., `us-west1-gcp`) | – |
| `DATA_PATH` | Path to the folder containing source documents (PDF, txt, md…) | `./data` |

Create a `.env` file:

```dotenv
OPENAI_API_KEY=sk-xxxxxx
LLM_MODEL=gpt-3.5-turbo
VECTOR_STORE=faiss
DATA_PATH=./data
```

---

## Running the Chatbot

### 1. CLI mode (quick test)

```bash
python -m llamaindex_rag_chatbot.cli
```

You will be prompted for a question, and the bot will stream the answer.

### 2. FastAPI server (RESTful interface)

```bash
uvicorn llamaindex_rag_chatbot.api:app --host 0.0.0.0 --port 8000
```

**Endpoints**

- `POST /chat` – Body `{ "question": "..." }` → Returns streaming JSON with `answer`.
- `GET /health` – Health check.

---

## Example Interaction

```text
User:  What are the main benefits of Retrieval‑Augmented Generation?
Bot:  Retrieval‑Augmented Generation (RAG) combines a language model with an external knowledge source. The main benefits are:

1. **Up‑to‑date information** – the model can surface facts that were published after its training cut‑off.
2. **Reduced hallucinations** – grounding the answer in retrieved documents lowers the risk of fabricating content.
3. **Scalability** – you can index millions of documents without retraining the LLM.
4. **Domain specificity** – by feeding a domain‑specific corpus, the bot can answer highly specialized questions.
```

---

## Testing

The repository includes a minimal test suite based on `pytest`. To run the tests:

```bash
pytest -q
```

The tests cover:
- Document loading and indexing.
- Retriever correctness (top‑k relevance).
- End‑to‑end query flow with a mock LLM.

---

## Contributing

Contributions are welcome! Follow these steps:

1. **Fork** the repository.
2. **Create a feature branch** (`git checkout -b feat/your-feature`).
3. **Write tests** for any new functionality.
4. **Run the linter** (`ruff check .` and `ruff format .`).
5. **Submit a Pull Request** with a clear description of the changes.

Please adhere to the existing code style (PEP 8, type hints) and update the documentation when you add new public interfaces.

---

## License

This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.
