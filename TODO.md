# TODO

This file tracks tasks, improvements, and features planned for upcoming updates or releases of ContextReader.

> [!Note]
> This list is **not exhaustive** and may change over time.
> See [PROJECT_PLAN.md](PROJECT_PLAN.md) for the full implementation roadmap.

## v0.1.0 — Initial Release

### Backend

- [ ] FastAPI project scaffolding with async endpoints
- [ ] Document upload endpoint (PDF, DOCX, TXT)
- [ ] Document parsing (PyMuPDF, python-docx)
- [ ] Recursive character text splitting (1000 chars, 200 overlap)
- [ ] Batch embedding with Azure OpenAI `text-embedding-3-small`
- [ ] ChromaDB persistent storage with cosine similarity
- [ ] Retrieval endpoint with top-5 similarity search
- [ ] Retrieval guardrail (L2 distance threshold 0.75)
- [ ] Versioned system prompt templates
- [ ] SSE streaming chat endpoint with GPT-4o
- [ ] Inline source citations in responses
- [ ] Prompt injection detection (sanitizer)
- [ ] Token usage and cost tracking per request
- [ ] Retrieval precision evaluation logging
- [ ] Structured JSON logging (request ID, latency, tokens)
- [ ] Rate limit handling (429 retry-after)
- [ ] Unit tests with pytest

### Frontend

- [ ] Next.js 16 App Router with TypeScript strict mode
- [ ] ChatWindow component with message history
- [ ] FileUpload component with drag-and-drop
- [ ] SSE stream reader (`ReadableStream` + `fetch` + `getReader()`)
- [ ] SourceCitation component for chunk references
- [ ] TokenUsage component for cost display
- [ ] Responsive layout with Tailwind CSS
- [ ] API client (`lib/api.ts`)

### Infrastructure

- [ ] Docker Compose setup (frontend + backend)
- [ ] GitHub Actions CI (pytest, lint, type check)
- [ ] `.env.example` with all configuration variables

## Future Improvements

- [ ] Multi-document conversation (chat across multiple uploads)
- [ ] Conversation history persistence
- [ ] Document management UI (list, delete)
- [ ] Chunk preview in citation tooltips
- [ ] Dark mode toggle
- [ ] Export conversation as Markdown
- [ ] Azure Cosmos DB for conversation storage
- [ ] Authentication and multi-user support
