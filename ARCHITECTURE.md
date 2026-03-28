# Architecture

This document describes the system architecture, design decisions, and data flow for ContextReader.

## System Overview

ContextReader is a Retrieval-Augmented Generation (RAG) application with three main layers:

1. **Frontend** — Next.js 16 chat interface for document upload and conversation
2. **Backend** — FastAPI service handling ingestion, retrieval, and generation
3. **Storage** — ChromaDB for vector embeddings, Azure OpenAI for AI capabilities

```text
┌─────────────────┐     HTTP/SSE       ┌─────────────────────┐
│   Next.js 16    │ ◄───────────────►  │     FastAPI         │
│   (Frontend)    │                    │     (Backend)       │
│                 │                    │                     │
│  • Chat UI      │   POST /upload     │  • Ingestion        │
│  • File Upload  │ ────────────────►  │  • Retrieval        │
│  • SSE Reader   │   GET /chat (SSE)  │  • Streaming        │
│  • Citations    │ ◄────────────────  │  • Evaluation       │
│  • Token Usage  │                    │  • Cost Tracking    │
└─────────────────┘                    │  • Sanitization     │
                                       └──────────┬──────────┘
                                                   │
                                    ┌──────────────┼──────────────┐
                                    │              │              │
                              ┌─────▼─────┐  ┌────▼────┐  ┌─────▼─────┐
                              │  ChromaDB │  │ GPT-4o  │  │ Embedding │
                              │ (Vectors) │  │  (Gen)  │  │  (Embed)  │
                              └───────────┘  └─────────┘  └───────────┘
```

## Data Flow

### Document Ingestion

```text
File Upload (PDF/DOCX/TXT)
    │
    ▼
Document Parser (PyMuPDF / python-docx / plain text)
    │
    ▼
Text Extraction (raw text content)
    │
    ▼
Chunking (RecursiveCharacterTextSplitter: 1000 chars, 200 overlap)
    │
    ▼
Batch Embedding (Azure OpenAI text-embedding-3-small)
    │
    ▼
Storage (ChromaDB PersistentClient, cosine similarity space)
```

### Query and Retrieval

```text
User Query
    │
    ▼
Input Sanitizer (prompt injection detection + length check)
    │
    ▼
Query Embedding (text-embedding-3-small)
    │
    ▼
Similarity Search (ChromaDB, top 5 results)
    │
    ▼
Guardrail Filter (discard chunks with L2 distance > 0.75)
    │
    ├── All chunks filtered → "I don't have enough information"
    │
    ▼
Prompt Assembly (versioned system prompt + context + query)
    │
    ▼
Streamed Generation (GPT-4o via SSE)
    │
    ▼
Response with Inline Citations + Token Usage
```

## Component Design

### Backend Modules

| Module | Responsibility |
| --- | --- |
| `main.py` | FastAPI app setup, route definitions, CORS configuration |
| `ingestion.py` | Document parsing, text chunking, batch embedding, ChromaDB storage |
| `retrieval.py` | Similarity search, guardrail filtering, prompt assembly |
| `streaming.py` | SSE streaming response handler using `AsyncAzureOpenAI` |
| `evaluation.py` | Retrieval precision logging for quality monitoring |
| `cost_tracker.py` | Token counting and cost estimation per request |
| `sanitizer.py` | Prompt injection detection and input validation |
| `prompts/` | Versioned system prompt templates (v1, v2, etc.) |

### Frontend Components

| Component | Responsibility |
| --- | --- |
| `ChatWindow` | Message history display with streaming token rendering |
| `FileUpload` | Drag-and-drop upload with file type validation |
| `SourceCitation` | Expandable citation blocks showing source chunks |
| `TokenUsage` | Token count and cost display per response |
| `lib/api.ts` | API client with `fetch` + `ReadableStream` for SSE |

## Design Decisions

### Why ChromaDB Over Other Vector Stores?

- **Zero infrastructure** — Runs as an embedded library with persistent storage
- **Python-native** — First-class Python SDK, no separate server process needed
- **Development friendly** — Simple API, easy to set up and debug locally
- **Sufficient for portfolio scope** — Handles thousands of documents efficiently

### Why SSE Over WebSockets?

- **Simpler protocol** — One-directional streaming fits the chat response pattern
- **HTTP-native** — Works through standard proxies, load balancers, and CDNs
- **Browser support** — `ReadableStream` + `getReader()` provides full control
- **Stateless backend** — No persistent connection management required

### Why Versioned Prompt Templates?

- **Reproducibility** — Pin a specific prompt version per deployment
- **A/B testing** — Compare response quality across prompt versions
- **Audit trail** — Track which prompt generated which responses in logs
- **Safe iteration** — Modify prompts without risking production regressions

### Why Batch Embedding?

- **Cost efficiency** — Single API call per document instead of per-chunk
- **Latency reduction** — One round trip vs. many sequential calls
- **Rate limit friendly** — Consumes fewer API calls against Azure OpenAI quotas

### Why Input Sanitization?

- **Prompt injection defense** — Detects adversarial patterns in user queries
- **Input validation** — Enforces length limits to prevent abuse
- **Logged events** — Guardrail triggers are captured in structured logs for monitoring

## Scalability Considerations

This architecture is designed for a portfolio project with moderate scale.
For production deployment at larger scale, consider:

- **Vector store** — Migrate from embedded ChromaDB to a managed service (Azure AI Search, Pinecone)
- **Embedding cache** — Cache embeddings for repeated queries
- **Horizontal scaling** — Run multiple FastAPI instances behind a load balancer
- **Async queuing** — Use background tasks for document ingestion
- **CDN** — Serve the Next.js frontend from a CDN edge network
