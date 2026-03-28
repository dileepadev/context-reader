# Project Plan

This document outlines the implementation roadmap, milestones, and definition of done for ContextReader.

## Vision

Build a production-grade RAG application that demonstrates end-to-end document intelligence: from file upload through semantic retrieval to streamed, cited AI responses.

## Milestones

### Milestone 1 — Backend Foundation

**Goal:** A working FastAPI backend that ingests documents, stores embeddings, and returns relevant chunks.

- [ ] Project scaffolding (FastAPI app, Docker setup, CI pipeline)
- [ ] Environment configuration with `pydantic-settings` and `.env`
- [ ] `/health` endpoint
- [ ] Document upload endpoint (`/upload`) accepting PDF, DOCX, TXT
- [ ] Document parsing with PyMuPDF (PDF) and python-docx (DOCX)
- [ ] Recursive character text splitting (chunk size 1000, overlap 200)
- [ ] Batch embedding with Azure OpenAI `text-embedding-3-small`
- [ ] ChromaDB persistent storage with cosine similarity
- [ ] Retrieval endpoint returning top 5 chunks with distances
- [ ] Structured JSON logging (request ID, latency, tokens)

### Milestone 2 — Retrieval and Generation

**Goal:** End-to-end chat pipeline with streamed responses and source citations.

- [ ] Retrieval guardrail filtering (L2 distance threshold 0.75)
- [ ] Versioned system prompt templates (`prompts/v1_system.txt`, `v2_system.txt`)
- [ ] Prompt assembly with retrieved context and user query
- [ ] SSE streaming endpoint (`/chat`) using `AsyncAzureOpenAI`
- [ ] Inline source citations in generated responses
- [ ] Token usage tracking per request
- [ ] Cost estimation per request
- [ ] Prompt injection detection via input sanitizer
- [ ] Evaluation logging (retrieval precision)
- [ ] Rate limit handling with retry-after logic

### Milestone 3 — Frontend

**Goal:** A polished Next.js chat interface that streams responses and displays citations.

- [ ] Next.js 16 App Router scaffolding with TypeScript strict mode
- [ ] `ChatWindow` component with message history
- [ ] `FileUpload` component with drag-and-drop and file type validation
- [ ] SSE stream reader using `ReadableStream` + `fetch` + `getReader()`
- [ ] `SourceCitation` component showing chunk references
- [ ] `TokenUsage` component displaying token count and estimated cost
- [ ] Responsive layout with Tailwind CSS
- [ ] Error handling and loading states
- [ ] API client in `lib/api.ts`

### Milestone 4 — Integration and Polish

**Goal:** Fully containerized, tested, and documented application.

- [ ] Docker Compose setup (frontend + backend + ChromaDB volume)
- [ ] GitHub Actions CI (pytest, lint, type check)
- [ ] Backend unit tests (ingestion, retrieval, sanitizer, cost tracker)
- [ ] Frontend lint and type check passing
- [ ] End-to-end manual testing with sample documents
- [ ] `DEPLOYMENT.md` with full setup instructions
- [ ] `ARCHITECTURE.md` with design decisions
- [ ] README with quick start, API reference, and project overview
- [ ] `CHANGELOG.md` updated for v0.1.0

## Implementation Details

### Chunking Strategy

| Parameter | Value | Rationale |
| --- | --- | --- |
| Chunk size | 1000 characters | Balances context window usage with retrieval granularity |
| Chunk overlap | 200 characters | Preserves context across chunk boundaries |
| Splitter | `RecursiveCharacterTextSplitter` | Respects paragraph and sentence boundaries |

### Embedding Configuration

- Model: `text-embedding-3-small` (1536 dimensions)
- Batch size: Process all chunks per document in a single API call
- Storage: ChromaDB `PersistentClient` with `hnsw:space = cosine`

### Retrieval Parameters

- Top K: 5 chunks per query
- Distance metric: L2 (ChromaDB default), with cosine space configured
- Guardrail threshold: 0.75 — chunks above this distance are filtered out
- When all chunks are filtered, respond with "I don't have enough information to answer"

### Streaming

- Protocol: Server-Sent Events (SSE) over HTTP
- Backend: FastAPI `StreamingResponse` with `async for` over Azure OpenAI completion chunks
- Frontend: `fetch` + `ReadableStream` + `getReader()` — not WebSockets

### Security

- Prompt injection detection on all user inputs before processing
- Input length limit (configurable via `MAX_INPUT_LENGTH`)
- No secrets in code — all credentials via `.env`
- CORS configured for frontend origin only

## Definition of Done

- [ ] User can upload a PDF, DOCX, or TXT file
- [ ] User can ask a question and receive a streamed answer
- [ ] Every answer includes inline source citations
- [ ] Low-confidence retrievals are filtered (no hallucinated answers)
- [ ] Token usage and cost are displayed per response
- [ ] Prompt injection attempts are detected and blocked
- [ ] All backend tests pass (`pytest tests/ -v`)
- [ ] All frontend checks pass (`npm run lint && npx tsc --noEmit`)
- [ ] Application runs via `docker-compose up --build`
- [ ] Structured logs capture every request with latency and token counts
