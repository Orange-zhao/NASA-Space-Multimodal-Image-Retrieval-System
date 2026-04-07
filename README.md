# NASA-Space-Multimodal-Image-Retrieval-System

A multimodal information retrieval system for NASA deep-space imagery, 
combining vector search with LLM-based summarization.

![Python](https://img.shields.io/badge/Python-3.11-blue)
![FastAPI](https://img.shields.io/badge/FastAPI-0.111-green)
![Docker](https://img.shields.io/badge/Docker-Compose-blue)

## Overview

This system allows users to search NASA's deep space image archive using 
either scientific descriptions or visual appearance. It supports two 
retrieval modes and an optional AI explanation feature powered by Llama 3.

## Features

- **Scientific text retrieval (BERT)** — search by scientific concept 
  using semantic embeddings
- **Visual similarity search (CLIP)** — find images by visual appearance 
  using OpenCLIP embeddings
- **RAG-powered AI summaries (Llama 3)** — automatically generate 
  scientific overviews for retrieved results
- **Interactive UI (Streamlit)** — clean web interface with search mode 
  controls and image visualization
- **Persistent vector store (ChromaDB)** — pre-computed embeddings for 
  fast similarity search

## Architecture
User (Browser)
↕
Streamlit Frontend  (port 8501)
↕ HTTP
FastAPI Backend     (port 8001)
↕                    ↕
ChromaDB            Ollama / Llama 3
(port 8000)         (port 11434)

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Frontend | Streamlit |
| Backend | FastAPI + Uvicorn |
| Text Embeddings | sentence-transformers (all-MiniLM-L6-v2) |
| Visual Embeddings | OpenCLIP (ViT-B-32, laion2b) |
| Vector Database | ChromaDB |
| LLM | Llama 3 via Ollama |
| Containerization | Docker Compose |

## Project Structure
multimodal-retrieval/<br>
├── backend/<br>
│   ├── main.py        # FastAPI app: search & RAG endpoints<br>
│   ├── requirements.txt<br>
│   └── Dockerfile<br>
├── frontend/<br>
│   ├── app.py          # Streamlit UI<br>
│   ├── requirements.txt<br>
│   └── Dockerfile<br>
├── notebook/<br>
│   ├── 00_data_scrpy.ipynb       # NASA data collection<br>
│   ├── 01_Embedding_Indexing.ipynb # Embedding generation & indexing<br>
│   └── data/<br>
├── storage/<br>
│   ├── chroma_db/        # ChromaDB persistent storage<br>
│   ├── ollama_models/    # Llama 3 model weights<br>
│   └── model_weights/    # HuggingFace & PyTorch cache<br>
└── docker-compose.yml<br>

## Quick Start

### Prerequisites
- Docker Desktop installed and running
- At least 8GB RAM recommended

### 1. Clone the repository
```bash
git clone https://github.com/yourusername/multimodal-retrieval.git
cd multimodal-retrieval
```

### 2. Create Docker network
```bash
docker network create nasa_network
```

### 3. Start all services
```bash
docker compose up --build
```

### 4. Pull Llama 3 model (first time only)
```bash
docker exec -it ollama_service ollama pull llama3
```

### 5. Open the app
Navigate to `http://localhost:8501` in your browser.

## Data Pipeline

The offline data pipeline runs in Jupyter notebooks before starting 
the system:

1. **`00_data_scrpy.ipynb`** — scrapes NASA image metadata via the 
   NASA Images API, filtering trusted sources (JPL, STScI)
2. **`01_Embedding_Indexing.ipynb`** — generates BERT and CLIP 
   embeddings, indexes them into ChromaDB collections:
   - `nasa_text_search` — BERT text embeddings
   - `nasa_image_search` — CLIP visual embeddings

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/search/text` | Semantic text search via BERT |
| POST | `/search/image` | Visual similarity search via CLIP |
| POST | `/search/rag` | Retrieval + Llama 3 summarization |
| GET | `/` | Health check |
