# Local PDF RAG System

A simple, local Retrieval-Augmented Generation (RAG) system that lets you ask questions about your PDF documents using AI.

## Features

- **100% Local & Private** - All processing happens on your machine
- **Free Embeddings** - Uses HuggingFace sentence-transformers (no API costs)
- **GPU Accelerated** - Supports CUDA for faster embeddings
- **LM Studio Integration** - Connect to any local LLM via LM Studio
- **Persistent Storage** - Save vector stores to skip re-processing

## Tech Stack

| Component | Technology |
|-----------|------------|
| Framework | LangChain |
| PDF Loading | PyPDFLoader |
| Text Splitting | RecursiveCharacterTextSplitter (chunk: 1000, overlap: 100) |
| Embeddings | HuggingFace `sentence-transformers/all-MiniLM-L6-v2` |
| Vector Store | FAISS |
| LLM | LM Studio (local OpenAI-compatible API) |

## Requirements

- Python 3.10+
- LM Studio with a loaded model
- (Optional) NVIDIA GPU with CUDA for faster embeddings

## Installation

```bash
pip install langchain langchain-community langchain-openai langchain-text-splitters langchain-huggingface langchain-core pypdf faiss-cpu sentence-transformers torch
```

For GPU support (requires compatible CUDA version):
```bash
pip install faiss-gpu
pip install torch --index-url https://download.pytorch.org/whl/cu124
```

## LM Studio Setup

1. Download and install [LM Studio](https://lmstudio.ai)
2. Download a model (e.g., `llama-3.2-3b-instruct`, `mistral-7b-instruct`)
3. Go to **Local Server** tab
4. Select your model and click **Start Server**
5. Verify server is running on `http://localhost:1234`

## Usage

### 1. Open the Notebook

Open `local_pdf_rag.ipynb` in Jupyter or VS Code.

### 2. Run All Setup Cells

Run cells 1-8 to import libraries and define functions.

### 3. Load Your PDF

Run the PDF loading cell - you'll be prompted to enter the path:

```
Enter the path to your PDF file: C:\Documents\my_document.pdf
```

**Tip:** Right-click your PDF in Windows Explorer → "Copy as path" → Paste

### 4. Ask Questions

**Single Question:**
```python
answer = rag.ask("What is the main topic of this document?")
```

**With Sources:**
```python
result = rag.ask_with_sources("What are the key findings?")
print(result["answer"])
print(result["sources"])
```

**Interactive Session:**
Run the interactive Q&A cell for continuous questioning:
```
❓ Your question: What does chapter 2 discuss?

🔍 Searching document...

============================================================
💬 ANSWER:
============================================================
Chapter 2 discusses the methodology used in the research,
including data collection methods and analysis techniques.
============================================================
```

Type `quit` to exit the session.

### 5. Save for Later (Optional)

Save the vector store to skip re-processing next time:

```python
rag.save_vector_store("my_pdf_index")
```

Load it later:
```python
rag = LocalPDFRAG.load_from_index("my_pdf_index")
```

## Project Structure

```
Pdf_RAG/
├── local_pdf_rag.ipynb    # Main notebook
├── my_pdf_index/          # Saved FAISS index (auto-generated)
│   ├── index.faiss
│   └── index.pkl
└── README.md
```

## Configuration

Edit these values in the notebook to customize:

```python
CHUNK_SIZE = 1000          # Characters per chunk
CHUNK_OVERLAP = 100        # Overlap between chunks
EMBEDDING_MODEL = "sentence-transformers/all-MiniLM-L6-v2"
LM_STUDIO_BASE_URL = "http://localhost:1234/v1"
EMBEDDING_BATCH_SIZE = 64  # Increase for faster GPU processing
```

## Troubleshooting

### "No GPU detected, using CPU"
- This is fine - embeddings will work on CPU
- LM Studio uses GPU for the LLM (the important part)
- For GPU embeddings, ensure PyTorch CUDA version matches your driver

### "Connection refused" to LM Studio
- Make sure LM Studio is running
- Check the Local Server is started on port 1234
- Verify with: `curl http://localhost:1234/v1/models`

### PDF path not found
- Use "Copy as path" from Windows Explorer
- Avoid manual typing - paths are case-sensitive

## License

MIT
