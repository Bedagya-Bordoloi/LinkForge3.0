# LinkForge — Graph-RAG & Predictive Knowledge Discovery for Scientific Literature

LinkForge is a scientific literature intelligence platform that converts research papers into an interactive knowledge graph and combines **Neo4j graph traversal**, **ChromaDB semantic retrieval**, and **LLM-powered Graph-RAG** to help users discover relationships, evidence, and hidden connections across scientific literature.

## Live Demo

**Frontend:**  
https://linkforge-silk.vercel.app

**Backend API:**  
https://linkforge-api-yil7.onrender.com

**API Documentation:**  
https://linkforge-api-yil7.onrender.com/docs

## Overview

LinkForge follows this pipeline:

```text
Research Papers
      │
      ▼
Europe PMC / Open Literature APIs
      │
      ▼
PDF / Abstract Parsing
      │
      ▼
Text Cleaning + Chunking
      │
      ▼
Groq LLM
      │
      ├── Entities
      ├── Relations
      ├── Confidence Scores
      └── Provenance Snippets
      │
      ▼
Entity Resolution + Relation Normalization
      │
      ├──────────────────────┐
      ▼                      ▼
   Neo4j                  ChromaDB
Knowledge Graph          Vector Store
      │                      │
      └──────────┬───────────┘
                 ▼
              Graph-RAG
                 │
       ┌─────────┼──────────┐
       ▼         ▼          ▼
    Search     Bridge     Chatbot
       │         │          │
       └─────────┼──────────┘
                 ▼
        React + 3D Graph UI
```

---

# Key Features

## Scientific Literature Processing

- Research paper retrieval through Europe PMC
- Open-access PDF retrieval when available
- Metadata extraction including DOI, PMID, PMCID, journal, year and authors
- PDF parsing using PyMuPDF
- Title + abstract fallback when a PDF is unavailable
- Structured text cleaning
- Sentence-aware text chunking
- Configurable chunk size, overlap and maximum chunks per paper

## LLM-Based Knowledge Extraction

- Groq LLM integration
- Subject–Relation–Object triplet extraction
- Entity type extraction
- Confidence score extraction
- Provenance/evidence snippet extraction
- Explicit-relation validation
- Relation normalization
- Entity normalization
- Duplicate triplet removal
- LLM retry and throttling handling

Example:

```text
Scientific text
      │
      ▼
"Curcumin inhibits NF-kB..."
      │
      ▼
{
  subject: "curcumin",
  relation: "INHIBITS",
  object: "NF-kB",
  confidence: 0.90,
  provenance_snippet: "..."
}
```

---

# Knowledge Graph

LinkForge uses **Neo4j** as the primary knowledge-graph database.

The graph stores:

- Scientific entities
- Diseases
- Chemicals
- Proteins
- Genes
- Processes
- Anatomy
- Organisms
- Papers
- Relationships between entities
- Paper provenance associated with relationships

### Graph capabilities

- Entity and relationship creation
- Entity canonicalization / merging
- Relationship normalization
- Multi-hop graph traversal
- Personalized PageRank (PPR)
- Graph-based relevance ranking
- Bridge / path search
- Evidence-linked relationships
- Paper-to-entity and entity-to-paper traversal

Example:

```text
Curcumin
   │
   ├── TREATS ──────────────► Alzheimer's disease
   │
   ├── INHIBITS ────────────► NF-kB
   │
   └── INHIBITS ────────────► inflammatory processes
```

---

# ChromaDB Semantic Retrieval

ChromaDB stores embedded paper chunks and provenance information for semantic retrieval.

The project uses:

```text
BAAI/bge-small-en-v1.5
```

for embeddings.

Features include:

- Text embedding generation
- Chunk storage
- Provenance storage
- Semantic/vector search
- Evidence retrieval
- Hybrid graph + semantic retrieval

The current repository contains the populated ChromaDB data used by the working demo.

---

# Graph-RAG

LinkForge combines graph reasoning with semantic retrieval.

The Graph-RAG pipeline performs:

```text
User Question
      │
      ▼
Graph Search
      │
      ├── Relevant entities
      ├── Relationships
      ├── Multi-hop paths
      └── Candidate papers
      │
      ▼
ChromaDB Evidence Retrieval
      │
      ▼
Paper / DOI Grounding
      │
      ▼
Groq LLM
      │
      ▼
Evidence-Grounded Answer
```

The chatbot is designed to answer using retrieved graph facts and paper excerpts rather than relying only on the model's internal knowledge.

---

# Link Prediction

LinkForge supports graph-based prediction of potentially interesting relationships.

### Current deployed/default method

When a trained GNN model is not available, the system uses:

**Adamic–Adar**

Predicted relationships are explicitly marked:

```json
{
  "predicted": true,
  "method": "adamic_adar"
}
```

The frontend visually distinguishes predicted links from extracted/published relationships.

### Optional GNN / GraphSAGE module

The repository also contains an optional PyTorch Geometric / GraphSAGE pipeline for:

- Graph export
- Dataset preparation
- Temporal splitting
- GraphSAGE training
- Link prediction
- Model loading

The GNN pipeline is **optional and separate from the default deployed prediction path**.

---

# Backend

LinkForge uses **FastAPI**.

Main API capabilities include:

```text
/api/search
/api/search/suggest
/api/search/bridge
/api/graph/stats
/api/graph/edge
/api/chat
/api/ingest/query
/api/ingest/upload
/api/ingest/jobs/{id}
/api/graph/reload-model
```

### API examples

Search:

```http
GET /api/search?q=curcumin&top_k=20&hops=3
```

Bridge search:

```http
GET /api/search/bridge?a=curcumin&b=alzheimer's%20disease&max_hops=6
```

Chat:

```http
POST /api/chat
Content-Type: application/json

{
  "question": "How is curcumin connected to Alzheimer's disease?"
}
```

---

# Frontend

The frontend is built with:

- React
- Vite
- Tailwind CSS
- react-force-graph-3d
- Three.js

### Frontend features

- Search bar
- Search autocomplete
- Interactive 3D graph
- Entity and relationship visualization
- Real vs predicted relationship visualization
- Node interaction
- Edge interaction
- Paper list
- Entity highlighting in paper snippets
- DOI/source display
- Bridge Search
- Graph-RAG chatbot
- Ingestion interface

### 3D graph

The graph is rendered in the browser using:

```text
react-force-graph-3d
```

Extracted relationships and predicted relationships are visually distinguished.

---

# Local Development

## Requirements

Recommended:

- Python 3.11
- Node.js + npm
- Neo4j Aura or local Neo4j
- Groq API key

## 1. Clone the repository

```bash
git clone https://github.com/Bedagya-Bordoloi/LinkForge3.0.git
cd linkforge
```

## 2. Create the Python virtual environment

### Windows PowerShell

```powershell
py -3.11 -m venv venv
.\venv\Scripts\Activate.ps1
```

### Linux / macOS

```bash
python3.11 -m venv venv
source venv/bin/activate
```

## 3. Install backend dependencies

```bash
python -m pip install -r requirements.txt
```

For development/testing:

```bash
python -m pip install -r requirements-dev.txt
```

For the optional GNN module:

```bash
python -m pip install -r requirements-gnn.txt
```

## 4. Configure environment variables

Copy:

```bash
cp .env.example .env
```

On Windows:

```powershell
Copy-Item .env.example .env
```

Configure the required values:

```env
NEO4J_URI=neo4j+s://<your-aura-instance>.databases.neo4j.io
NEO4J_USER=<your-user>
NEO4J_PASSWORD=<your-password>
NEO4J_DATABASE=<your-database>

GROQ_API_KEY=<your-groq-api-key>
LLM_PROVIDER=groq

FRONTEND_URL=http://localhost:5173
ADMIN_API_KEY=<strong-random-key>
```

**Never commit `.env`.**

## 5. Initialize the database

```bash
python -m scripts.init_db
```

This creates the required constraints and indexes.

### Optional synthetic demo data

```bash
python -m scripts.seed_demo --reset
```

The demo records are synthetic placeholders and are not real publications.

## 6. Start the backend

From the project root:

```bash
python -m uvicorn backend.main:app --reload --port 8000
```

Backend:

```text
http://127.0.0.1:8000
```

API documentation:

```text
http://127.0.0.1:8000/docs
```

Health check:

```text
http://127.0.0.1:8000/health
```

Expected:

```json
{
  "status": "ok",
  "neo4j": "ok",
  "llm": "ok"
}
```

## 7. Start the frontend

In another terminal:

```bash
cd frontend
npm install
npm run dev
```

Frontend:

```text
http://localhost:5173
```

During local development, Vite proxies `/api` requests to:

```text
http://localhost:8000
```

---

# Real Paper Ingestion

Example:

```bash
python -m scripts.run_pipeline \
  --query "curcumin alzheimer" \
  --source europe_pmc \
  --limit 10
```

The API can also trigger ingestion:

```http
POST /api/ingest/query
```

Example payload:

```json
{
  "query": "curcumin alzheimer",
  "source": "europe_pmc",
  "limit": 10
}
```

PDF upload is supported through:

```http
POST /api/ingest/upload
```

### PDF fallback

When an open-access PDF is unavailable, LinkForge falls back to:

```text
Title + Abstract
```

---

# Testing

Run the complete automated test suite:

```bash
python -m pytest -q
```

Current status:

```text
28 tests passed
```

The test suite covers:

- API behaviour
- Pipeline logic
- Graph-RAG behaviour
- Graph algorithms
- Provenance handling
- Entity/relationship processing

The tests use fakes/mocks where appropriate so the full suite does not require a live external LLM or Neo4j connection.

---

# Deployment

## Frontend

The production frontend is deployed on **Vercel**.

Production URL:

```text
https://linkforge-silk.vercel.app
```

Build configuration:

```text
Framework: Vite
Root Directory: frontend
Build Command: npm run build
Output Directory: dist
```

Set:

```env
VITE_API_URL=https://linkforge-api-yil7.onrender.com
```

## Backend

The production backend is deployed on **Render** using:

```text
Dockerfile
render.yaml
```

Production API:

```text
https://linkforge-api-yil7.onrender.com
```

Render environment variables include:

```env
NEO4J_URI
NEO4J_USER
NEO4J_PASSWORD
NEO4J_DATABASE
GROQ_API_KEY
LLM_PROVIDER
FRONTEND_URL
ADMIN_API_KEY
CONTACT_EMAIL
```

## Neo4j

The deployed application uses **Neo4j Aura** for the persistent knowledge graph.

## ChromaDB

The current demo includes a populated ChromaDB snapshot used for semantic and provenance retrieval.

For long-term production deployments, persistent vector storage should be used rather than relying on an ephemeral application filesystem.

---

# Security

- Never commit `.env`
- Never expose Neo4j passwords or Groq API keys
- Set `ADMIN_API_KEY` before public deployment
- Write/ingestion/model-management endpoints use the admin API key
- Read endpoints are rate-limited
- Paper content is treated as untrusted input to the LLM
- Keep Python and Node dependencies updated
- Only accept PDF uploads from trusted users in administrative workflows

---

# Project Structure

```text
LinkForge/
│
├── backend/
│   ├── api/
│   │   ├── endpoints/
│   │   ├── jobs.py
│   │   ├── router.py
│   │   └── security.py
│   │
│   ├── core/
│   │   ├── neo4j_client.py
│   │   ├── vector_client.py
│   │   ├── pipeline.py
│   │   ├── graph_rag.py
│   │   ├── analytics.py
│   │   └── services.py
│   │
│   └── main.py
│
├── ai_engine/
│   ├── extraction/
│   │   ├── llm_extractor.py
│   │   └── prompts.py
│   ├── resolution/
│   └── gnn/
│
├── data_pipeline/
│   ├── scrapers/
│   ├── parsers/
│   └── storage/
│
├── frontend/
│   ├── src/
│   ├── package.json
│   └── vite.config.js
│
├── scripts/
│   ├── init_db.py
│   ├── run_pipeline.py
│   ├── seed_demo.py
│   └── export_graph.py
│
├── notebooks/
├── tests/
├── research_paper/
│
├── Dockerfile
├── docker-compose.yml
├── render.yaml
├── requirements.txt
├── requirements-dev.txt
└── requirements-gnn.txt
```

---

# Example Queries

### Search

```text
curcumin
```

### Related scientific concepts

```text
microglial activation
```

### Bridge search

```text
microglial activation → neuroinflammation
```

### Graph-RAG question

```text
How is curcumin connected to Alzheimer's disease?
```

---

# Architecture Summary

| Layer | Technology |
|---|---|
| Paper source | Europe PMC |
| PDF parsing | PyMuPDF |
| Text processing | Custom Python pipeline |
| LLM | Groq |
| Embeddings | BAAI/bge-small-en-v1.5 |
| Knowledge graph | Neo4j |
| Vector database | ChromaDB |
| Graph ranking | Personalized PageRank |
| Path search | Multi-hop graph traversal |
| Link prediction | Adamic–Adar fallback |
| Optional GNN | PyTorch Geometric / GraphSAGE |
| Backend | FastAPI |
| Frontend | React + Vite |
| 3D visualization | react-force-graph-3d |
| Backend deployment | Render |
| Frontend deployment | Vercel |

---

# Current Project Status

LinkForge currently provides a working end-to-end system for:

```text
Scientific literature
        ↓
LLM knowledge extraction
        ↓
Knowledge graph + vector store
        ↓
Graph traversal + semantic retrieval
        ↓
Graph-RAG
        ↓
Interactive 3D scientific knowledge graph
```

The production deployment has been validated end-to-end, with the backend connected to Neo4j Aura and the frontend connected through the deployed FastAPI API.

Automated test suite:

```text
28 / 28 passing
```

---

# Future Work

Potential extensions include:

- Larger-scale literature ingestion
- Persistent production vector storage
- Improved GNN training and evaluation
- More rigorous link-prediction benchmarks
- Retrieval and Graph-RAG evaluation datasets
- Temporal back-testing of predicted relationships
- User accounts and access control
- Additional scientific literature sources
```
