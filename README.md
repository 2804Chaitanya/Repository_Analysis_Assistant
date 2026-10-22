# 🔍 Repository Analysis Assistant (Codebase Intelligence)

<div align="center">

[![Python](https://img.shields.io/badge/Python-3.11+-blue.svg?logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-009688.svg?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/React-18-61DAFB.svg?logo=react&logoColor=black)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-6.0+-646CFF.svg?logo=vite&logoColor=white)](https://vitejs.dev/)
[![ChromaDB](https://img.shields.io/badge/Vector_DB-ChromaDB-orange.svg)](https://www.trychroma.com/)
[![Groq](https://img.shields.io/badge/LLM-Groq_LLaMA--3.3--70B-f55036.svg)](https://groq.com/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED.svg?logo=docker&logoColor=white)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**AI-powered semantic code search, retrieval-augmented generation (RAG) Q&A, and automated codebase summarization for any software repository.**

[Live Demo](https://huggingface.co/spaces/Dharanidhar1709/Repository-Analysis-Assistant) • [Key Features](#-key-features) • [System Architecture](#-system-architecture) • [Getting Started](#-getting-started) • [API Reference](#-api-reference)

</div>

---

## 📖 Overview

**Repository Analysis Assistant** (Codebase Intelligence) is an end-to-end full-stack application designed to make navigating, understanding, and onboarding onto codebases effortless. Developers can either upload a project `.zip` archive or simply paste any public **GitHub repository URL**.

The system parses and semantically chunks source files across multiple programming languages, enriches chunks with structural AST metadata, computes dense vector embeddings using SentenceTransformers, and indexes them in an isolated ChromaDB vector collection. Developers can then chat with their repository using Groq's high-speed **LLaMA-3.3-70B** model, run semantic vector searches across functions and classes, generate high-level architectural summaries, and automatically generate missing docstrings.

---

## ✨ Key Features

- **🌐 Zero-Install GitHub URL Ingestion**: Clone and index any public GitHub repository directly in seconds—no manual downloading or packaging required.
- **📦 ZIP Archive Upload**: Seamlessly drag-and-drop or select `.zip` archives of proprietary or local codebases.
- **🧠 Multi-Language AST & Syntax Chunking**:
  - **Python**: Full Abstract Syntax Tree (`ast`) traversal extracting functions, classes, methods, docstrings, and call dependencies.
  - **Jupyter Notebooks**: Indexes `.ipynb` code cells independently with line context.
  - **Multi-language support**: Javascript, TypeScript, React JSX/TSX, C, C++, Java, Go, Rust, Ruby, and Markdown.
- **⚡ Enriched Semantic Embeddings**:
  - Employs `sentence-transformers/all-MiniLM-L6-v2` to create rich vector representations.
  - Prepends intelligent structural hints (e.g., API route handlers, data schemas, utility functions, parent classes, dependencies) to enhance semantic relevance.
- **🔒 Multi-Session Isolation**:
  - Each upload/clone generates a isolated ChromaDB collection identifier (`{upload_id}_{repo_name}`).
  - Prevents multi-user data collision and ensures strict workspace privacy.
- **💬 Retrieval-Augmented Generation (RAG) Q&A**:
  - Queries vector space for top-$k$ relevant code fragments with cosine similarity.
  - Synthesizes accurate, contextualized answers using **Groq LLaMA-3.3-70B-Versatile** (with fallback to `llama-3.1-8b-instant`).
- **📊 AI Repository Summarization**:
  - Generates executive-level architecture overviews, technology breakdown, and structural explanations.
  - Uses smart sampling across distinct files to remain strictly within LLM token limits while maintaining broad coverage.
- **📝 Automated Docstring Generation**:
  - Automatically identifies functions and methods lacking documentation, generates standard Google/Sphinx style docstrings with LLM, updates source files, and dynamically re-indexes the vector store.
- **🎨 Dual UI Themes (SaaS & Hacker)**:
  - Clean **SaaS Light** editorial theme with Instrument Serif & DM Sans typography.
  - Cyberpunk **Hacker Dark** theme with JetBrains Mono, glowing accents, and terminal vibes.

---

## 🏗️ System Architecture

```
                       ┌───────────────────────────────┐
                       │   GitHub URL  /  ZIP Upload   │
                       └───────────────┬───────────────┘
                                       │
                        Clone / Unpack & Normalize
                                       │
                                       ▼
                       ┌───────────────────────────────┐
                       │    Multi-Language Scanner     │
                       │ (Python AST, Notebooks, C/JS) │
                       └───────────────┬───────────────┘
                                       │ Chunks with Metadata
                                       ▼
                       ┌───────────────────────────────┐
                       │   Context Enrichment Engine   │
                       │ (Tags, Callgraph Dependencies)│
                       └───────────────┬───────────────┘
                                       │ Text with Structural Hints
                                       ▼
                       ┌───────────────────────────────┐
                       │   SentenceTransformer Model   │
                       │     (all-MiniLM-L6-v2)        │
                       └───────────────┬───────────────┘
                                       │ Vectors (384-d)
                                       ▼
                       ┌───────────────────────────────┐
                       │       ChromaDB Storage        │
                       │  (Isolated Session Collection)│
                       └───────────────┬───────────────┘
                                       │
               ┌───────────────────────┴───────────────────────┐
               │                                               │
               ▼                                               ▼
     ┌───────────────────┐                           ┌───────────────────┐
     │  Semantic Search  │                           │   RAG / Q&A LLM   │
     │ Top-K similarity  │                           │ Groq LLaMA-3.3-70B│
     └───────────────────┘                           └───────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology | Details |
|---|---|---|
| **Backend Framework** | [FastAPI](https://fastapi.tiangolo.com/) | Asynchronous Python REST API with lifespan model pre-loading |
| **Server** | [Uvicorn](https://www.uvicorn.org/) | Lightning-fast ASGI production web server |
| **LLM Provider** | [Groq Cloud](https://groq.com/) | High-throughput inference engine using `llama-3.3-70b-versatile` |
| **Embeddings** | [SentenceTransformers](https://www.sbert.net/) | `all-MiniLM-L6-v2` (384-dimensional dense vectors) |
| **Vector Database** | [ChromaDB](https://www.trychroma.com/) | Persistent vector store with Cosine distance metric |
| **Code Parsing** | Python `ast`, Regex | Abstract syntax tree traversal, dependency extraction, notebook parsing |
| **Frontend UI** | [React 18](https://react.dev/) + [Vite](https://vitejs.dev/) | Single-page application with responsive state management |
| **Styling** | Modern CSS-in-JS | Dual-mode responsive design (SaaS Light & Hacker Dark) |
| **Containerization** | [Docker](https://www.docker.com/) | Multi-stage Python 3.11-slim + Node.js runtime image |

---

## 🚀 Getting Started

### Prerequisites

Ensure you have the following installed on your machine:
- **Python 3.10+** (Python 3.11 recommended)
- **Node.js 18+** & **npm**
- **Git**
- A free **Groq API Key** (obtainable from [Groq Console](https://console.groq.com/))

### 1. Clone the Repository

```bash
git clone https://github.com/2804Chaitanya/Repository_Analysis_Assistant.git
cd Repository_Analysis_Assistant
```

### 2. Set Up Environment Variables

Create a `.env` file in the project root:

```bash
cp .env.example .env
```

Open `.env` and add your Groq API key:

```env
GROQ_API_KEY=gsk_your_actual_groq_api_key_here
```

### 3. Backend Setup

Create and activate a virtual environment:

```bash
# macOS/Linux
python3 -m venv venv
source venv/bin/activate

# Windows
python -m venv venv
venv\Scripts\activate
```

Install backend dependencies:

```bash
pip install -r requirements.txt
```

### 4. Frontend Build

Build the React frontend client so FastAPI can serve the static distribution:

```bash
cd frontend
npm install
npm run build
cd ..
```

### 5. Run the Application

Start the FastAPI server:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Once running, navigate to:
- **Web Application**: [http://localhost:8000](http://localhost:8000)
- **Interactive Swagger API Docs**: [http://localhost:8000/docs](http://localhost:8000/docs)
- **ReDoc Documentation**: [http://localhost:8000/redoc](http://localhost:8000/redoc)

---

## 🐳 Docker Deployment

You can build and deploy the application as a self-contained container:

```bash
# Build the Docker image
docker build -t repository-analysis-assistant .

# Run the container with your Groq API key
docker run -p 8000:8000 -e GROQ_API_KEY="your_groq_api_key_here" repository-analysis-assistant
```

Access the application in your browser at `http://localhost:8000`.

---

## 📡 API Reference

The backend provides a comprehensive RESTful API under the `/repo` prefix:

| Method | Endpoint | Description | Request Body / Parameters |
|---|---|---|---|
| `GET` | `/repo/health` | Health check for the service | None |
| `POST` | `/repo/upload` | Upload `.zip` archive, index, and return collection ID | `multipart/form-data` (`file`) |
| `POST` | `/repo/index-url` | Clone and index public GitHub repository | `{"url": "https://github.com/user/repo"}` |
| `POST` | `/repo/ask` | Ask questions over indexed codebase using RAG | `{"query": "...", "collection_name": "...", "k": 5}` |
| `POST` | `/repo/search` | Semantic code search with cosine similarity | `{"query": "...", "collection_name": "...", "k": 5}` |
| `POST` | `/repo/summarize` | AI-generated architectural repository summary | `{"collection_name": "..."}` |
| `POST` | `/repo/generate-docstrings` | Generate missing docstrings and re-index | `{"path": ".", "collection_name": "..."}` |
| `POST` | `/repo/scan` | Scan directory and return supported code files | Query param: `path` |
| `POST` | `/repo/chunk` | Chunk a specific file into functions/classes | Query param: `path` |
| `DELETE`| `/repo/session/{upload_id}` | Clean up ChromaDB vector collections | Path param: `upload_id` |

### Example cURL Requests

#### Ingest via GitHub URL
```bash
curl -X POST "http://localhost:8000/repo/index-url" \
     -H "Content-Type: application/json" \
     -d '{"url": "https://github.com/pallets/flask"}'
```

#### Ask a Question (RAG)
```bash
curl -X POST "http://localhost:8000/repo/ask" \
     -H "Content-Type: application/json" \
     -d '{
       "query": "How is authentication handled in this project?",
       "collection_name": "a1b2c3d4_flask",
       "k": 5
     }'
```

#### Semantic Search
```bash
curl -X POST "http://localhost:8000/repo/search" \
     -H "Content-Type: application/json" \
     -d '{
       "query": "vector embedding calculation",
       "collection_name": "a1b2c3d4_flask",
       "k": 3
     }'
```

---

## 📂 Project Directory Structure

```plaintext
Repository_Analysis_Assistant/
├── .env.example                # Example environment configuration
├── .gitignore                  # Git ignore rules for Python, Node, Chroma
├── Dockerfile                  # Container build recipe
├── README.md                   # Project documentation
├── requirements.txt            # Python dependencies
├── app/                        # FastAPI Backend Application
│   ├── main.py                 # Application entrypoint & static mount
│   ├── api/
│   │   └── repo_routes.py      # REST endpoint definitions
│   └── services/
│       ├── repo_loader.py      # Code file discovery & language mapping
│       ├── chunker.py          # Python AST & multi-language chunker
│       ├── embedder.py         # SentenceTransformers embedding model
│       ├── vector_store.py     # ChromaDB persistence & collections
│       ├── llm.py              # Groq LLaMA integration & RAG prompt
│       ├── summarizer.py       # File tree builder & repo summarizer
│       ├── git_handler.py      # Shallow Git cloning for remote repos
│       ├── zip_handler.py      # Safe ZIP extraction & temp cleanup
│       └── docstring_generator.py # AST docstring injector
└── frontend/                   # React + Vite Frontend Application
    ├── index.html              # HTML template
    ├── package.json            # Node dependencies and build scripts
    ├── vite.config.js          # Vite bundler configuration
    ├── public/                 # Static assets (favicons, SVGs)
    └── src/
        ├── main.jsx            # React root mount
        ├── App.jsx             # Main dashboard, dual themes & views
        ├── App.css             # Supplementary styling
        └── index.css           # Global reset & baseline styles
```

---

## ⚡ Large Repository Optimizations

Analyzing massive repositories with thousands of files introduces memory and LLM context challenges. This assistant solves them through:
1. **File Tree Capping**: Truncates file hierarchies at 100 entries in summary generation while retaining overall counts.
2. **Balanced Stratified Sampling**: Picks one representative chunk per unique file (up to 15 files) rather than multiple chunks from the same file for high-level summaries.
3. **Chunk Batching**: Batches embedding generations in sets of 16 to prevent RAM spikes on resource-constrained hosting tiers.
4. **Targeted Top-K Retrieval**: Limits context injection to high-relevance code blocks with cosine similarity thresholds.

---

## 🤝 Contributing

Contributions, issues, and feature suggestions are always welcome!
1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'feat: Add AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">
Built with ❤️ for developers who love clean, understandable code.
</div>
