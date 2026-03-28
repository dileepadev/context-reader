# Project Plan

This document is the single planning source for building ContextReader v0.1.0.

## Goal

Build a production-grade RAG application that supports document upload, semantic retrieval, and streamed answers with source citations.

## How to Use This Plan

- Use the phase overview to understand sequence and expected outcomes
- Use the detailed checklist to track implementation progress
- Use the definition of done as the release gate for v0.1.0
- Keep future enhancements in the backlog section only

## Phase Overview

| Phase | Objective | Exit Criteria |
| --- | --- | --- |
| 1. Backend Foundation | Ingest files and store retrievable embeddings | Upload, parse, chunk, embed, and retrieve paths working end-to-end |
| 2. Retrieval and Generation | Produce safe, streamed responses with citations | `/chat` streams answers with citations and guardrails enabled |
| 3. Frontend Experience | Deliver a usable chat UI with streaming and transparency | User can upload, chat, and inspect citations/token usage |
| 4. Integration and Release Readiness | Verify quality, docs, and deployment readiness | CI passes, docs are complete, and app runs through Docker Compose |

## Detailed Checklist

### Phase 1 - Backend Foundation

- [ ] FastAPI project scaffolding with async endpoints
- [ ] Environment configuration using `pydantic-settings` and `.env`
- [ ] `/health` endpoint
- [ ] Document upload endpoint (`/upload`) for PDF, DOCX, and TXT
- [ ] Document parsing with PyMuPDF and python-docx
- [ ] Recursive character text splitting (chunk size 1000, overlap 200)
- [ ] Batch embedding with Azure OpenAI `text-embedding-3-small`
- [ ] ChromaDB `PersistentClient` storage with cosine configuration
- [ ] Retrieval endpoint returning top-5 chunks with distance values
- [ ] Structured JSON logging (request ID, latency, tokens)

### Phase 2 - Retrieval and Generation

- [ ] Retrieval guardrail filtering (distance threshold 0.75)
- [ ] Versioned system prompts (`prompts/v1_system.txt`, `prompts/v2_system.txt`)
- [ ] Prompt assembly using retrieved context + user query
- [ ] SSE streaming endpoint (`/chat`) using singleton `AsyncAzureOpenAI`
- [ ] Inline source citations in generated responses
- [ ] Token usage and cost tracking per request
- [ ] Prompt injection detection via sanitizer
- [ ] Retrieval evaluation logging (precision signals)
- [ ] Rate limit handling with retry-after logic for 429

### Phase 3 - Frontend Experience

- [ ] Next.js 16 App Router setup with TypeScript strict mode
- [ ] `ChatWindow` with message history and streaming state
- [ ] `FileUpload` with drag-and-drop and file validation
- [ ] SSE reader (`fetch` + `ReadableStream` + `getReader()`)
- [ ] `SourceCitation` component for chunk references
- [ ] `TokenUsage` component for token and cost visibility
- [ ] Responsive layout with Tailwind CSS
- [ ] API client implementation in `frontend/lib/api.ts`
- [ ] Clear loading, empty, and error states

### Phase 4 - Integration and Release Readiness

- [ ] Docker Compose setup (frontend + backend + persistent volume)
- [ ] GitHub Actions CI for backend tests and frontend checks
- [ ] Backend unit tests for ingestion, retrieval, sanitizer, and cost tracking
- [ ] Frontend lint and type checks passing
- [ ] End-to-end manual test using sample documents
- [ ] Documentation completion in `docs/ARCHITECTURE.md` and `docs/DEPLOYMENT.md`
- [ ] README accuracy for quick start and API overview
- [ ] Changelog update for v0.1.0

## Technical Baselines

### Chunking Strategy

| Parameter | Value | Rationale |
| --- | --- | --- |
| Chunk size | 1000 characters | Balances context quality and retrieval granularity |
| Chunk overlap | 200 characters | Preserves context across chunk boundaries |
| Splitter | `RecursiveCharacterTextSplitter` | Better structural splits than naive fixed windows |

### Embedding and Storage

- Embedding model: `text-embedding-3-small`
- Embedding mode: batch per document, not per chunk request loops
- Vector store: ChromaDB `PersistentClient(path="./chroma_db")`
- Similarity space: cosine (`metadata={"hnsw:space": "cosine"}`)

### Retrieval and Guardrails

- Top K: 5 chunks per query
- Distance threshold: 0.75
- Fallback behavior: return an insufficient-context response when nothing passes threshold

### Streaming Contract

- Protocol: Server-Sent Events over HTTP
- Backend: FastAPI `StreamingResponse` with async chunk forwarding
- Frontend: `fetch` + stream reader (no WebSockets)

### Security and Reliability

- Sanitizer checks for prompt injection on user input
- Configurable maximum input length (`MAX_INPUT_LENGTH`)
- No hardcoded secrets, all credentials via environment variables
- Retry-after handling for Azure OpenAI 429 responses

## Definition of Done (v0.1.0)

- [ ] User can upload a PDF, DOCX, or TXT file
- [ ] User can ask a question and receive a streamed answer
- [ ] Every answer includes inline source citations
- [ ] Low-confidence retrievals are filtered
- [ ] Token usage and cost are displayed per response
- [ ] Prompt injection attempts are detected and blocked
- [ ] Backend tests pass (`python -m pytest tests/ -v`)
- [ ] Frontend checks pass (`npm run lint && npx tsc --noEmit`)
- [ ] Application runs via `docker-compose up --build`
- [ ] Structured logs include request metadata, latency, and tokens

## Post-v0.1.0 Backlog

- [ ] Multi-document conversation support
- [ ] Conversation history persistence
- [ ] Document management UI (list, delete)
- [ ] Citation tooltip chunk previews
- [ ] Dark mode toggle
- [ ] Export conversation as Markdown
- [ ] Azure Cosmos DB for conversation storage
- [ ] Authentication and multi-user support
