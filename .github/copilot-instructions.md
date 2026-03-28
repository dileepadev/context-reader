## Project

**ContextReader** — a full-stack RAG (Retrieval-Augmented Generation) document chat application.

- **Stack**: Python · FastAPI · Next.js · TypeScript · Azure OpenAI · ChromaDB · Docker
- Users upload documents (PDF, DOCX, TXT) and have natural language conversations with them.
- Pipeline: ingest → chunk → embed → retrieve → generate (streamed) with inline source citations.
- Part of a 4-project AI Engineer portfolio (Project 1).

## Architecture

```
backend/          # Python — FastAPI, ingestion, retrieval, streaming, evaluation
  prompts/        # Versioned system prompt templates (v1_system.txt, v2_system.txt)
  tests/          # pytest unit tests
  logs/           # Structured JSON logs + cost tracking
frontend/         # Next.js 16 — TypeScript, Tailwind CSS
  app/            # App router pages
  components/     # ChatWindow, FileUpload, SourceCitation, TokenUsage
  lib/            # API client (fetch + SSE)
docs/             # Project-specific documentation
  ARCHITECTURE.md # System design and data flow
  DEPLOYMENT.md   # Environment setup and deployment guide
  PROJECT_PLAN.md # Source of truth for delivery phases and task tracking
```

`docs/PROJECT_PLAN.md` is the single planning source — use it to track implementation progress.

## Tech Conventions

### Python (backend)

- Use `async` FastAPI endpoints.
- Load config via `pydantic-settings` from `.env`.
- Structured JSON logging — every request logs `request_id`, `endpoint`, `tokens_used`, `latency_ms`, `prompt_version`, `guardrail_triggered`.
- Reuse a singleton `AsyncAzureOpenAI` client — never recreate per request.
- Handle `429` (Rate Limit) with retry-after logic.
- Batch embedding calls — do not call the API per chunk individually.
- System prompts live in `backend/prompts/*.txt` — never hardcode prompt strings.

### TypeScript (frontend)

- Next.js 16 App Router with TypeScript strict mode.
- Use `ReadableStream` + `fetch` + `getReader()` for SSE streaming — not WebSockets.
- Tailwind CSS for styling.

### ChromaDB

- Use `PersistentClient(path="./chroma_db")` — not the in-memory client.
- ChromaDB returns L2 distance (lower = more similar) by default.
- For cosine similarity: `metadata={"hnsw:space": "cosine"}` on collection creation.

### Azure OpenAI

- Generation model: `gpt-4o` via `AZURE_OPENAI_DEPLOYMENT_NAME`.
- Embedding model: `text-embedding-3-small` via `AZURE_OPENAI_EMBEDDING_DEPLOYMENT`.
- Always use the latest Azure OpenAI SDK. Enable connection retries and preferred regions.

## Git Conventions

- **Commit messages**: Follow [COMMIT_MESSAGE_GUIDELINES.md](../COMMIT_MESSAGE_GUIDELINES.md) — format: `<type>(<scope>): <short message>`.
- **Branch names**: Follow [BRANCH_NAMING_GUIDELINES.md](../BRANCH_NAMING_GUIDELINES.md) — format: `<type>/x`.
- **PR titles**: Follow [PULL_REQUEST_GUIDELINES.md](../PULL_REQUEST_GUIDELINES.md) — format: `<type>(<branch>): <message> [#issue]`.
- **Changelog**: Follow [CHANGELOG.md](../CHANGELOG.md) — Added, Changed, Fixed, Removed.
- **Versioning**: Follow [VERSIONING.md](../VERSIONING.md) — SemVer.

## Key Rules

- Do not hardcode secrets — use environment variables via `.env`.
- Do not commit `.env`, `__pycache__/`, `node_modules/`, `.pytest_cache/`, `chroma_db/`.
- Update `CHANGELOG.md` when making notable changes.
- Report security vulnerabilities to `contact@dileepa.dev` per [SECURITY.md](../SECURITY.md).
