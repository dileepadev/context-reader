# Agent Instructions

This file provides instructions for AI coding agents (GitHub Copilot agent mode, etc.) working in this repository.

## Repository Summary

**ContextReader** is a full-stack RAG (Retrieval-Augmented Generation) document chat application.
Users upload documents (PDF, DOCX, TXT) and have natural language conversations with them.
Pipeline: ingest → chunk → embed → retrieve → generate (streamed) with inline source citations.

- **Backend**: Python, FastAPI, ChromaDB, Azure OpenAI
- **Frontend**: Next.js 16, TypeScript, Tailwind CSS
- **Infra**: Docker, docker-compose, GitHub Actions CI

## Validated Commands

```bash
# Backend tests
cd backend && python -m pytest tests/ -v

# Frontend lint + type check
cd frontend && npm run lint && npx tsc --noEmit

# Full stack (Docker)
docker-compose up --build

# Health check
curl http://localhost:8000/health
```

All commands must pass before opening a pull request.

## Project Structure

```text
backend/
  main.py              # FastAPI app, routes, CORS
  ingestion.py         # Chunking + embedding pipeline
  retrieval.py         # ChromaDB query + guardrail logic
  streaming.py         # SSE streaming handler
  evaluation.py        # Retrieval precision logging
  cost_tracker.py      # Token usage + cost per request
  sanitizer.py         # Prompt injection detection
  prompts/             # Versioned system prompt templates
  tests/               # pytest unit tests
  logs/                # Structured JSON logs + cost tracking
frontend/
  app/                 # Next.js App Router pages
  components/          # ChatWindow, FileUpload, SourceCitation, TokenUsage
  lib/                 # API client (fetch + SSE)
```

## Adding Backend Code

- New endpoints go in `backend/main.py`.
- New pipeline logic goes in the appropriate module (`ingestion.py`, `retrieval.py`, `streaming.py`).
- Tests go in `backend/tests/test_<module>.py`.
- Use `async` for all FastAPI endpoints.
- Load config via `pydantic-settings` from `.env`.
- System prompts live in `backend/prompts/*.txt` — never hardcode prompt strings.

## Adding Frontend Code

- Pages go in `frontend/app/`.
- Reusable components go in `frontend/components/`.
- API client logic goes in `frontend/lib/api.ts`.
- Use `ReadableStream` + `fetch` + `getReader()` for SSE streaming — not WebSockets.

## Key Constraints

- Do not hardcode secrets — use environment variables via `.env`.
- Do not commit `.env`, `__pycache__/`, `node_modules/`, `.pytest_cache/`, `chroma_db/`.
- Reuse the singleton `AsyncAzureOpenAI` client — never recreate per request.
- Batch embedding calls — do not call the Azure OpenAI API per chunk individually.
- ChromaDB: use `PersistentClient(path="./chroma_db")` — not the in-memory client.
- Update `CHANGELOG.md` when making notable changes.
- Follow conventions in `.github/copilot-instructions.md` for git, coding, and documentation standards.
