---
description: "Use when writing or editing Python backend code. Covers async patterns, FastAPI conventions, Azure OpenAI client usage, ChromaDB patterns, logging, and security."
applyTo: "backend/**/*.py"
---
# Python Backend Standards

## FastAPI
- All endpoints MUST be `async def`.
- Load config via `pydantic-settings` `BaseSettings` from `.env`.
- Return proper HTTP status codes and JSON error responses.
- Use dependency injection for shared resources (client, settings).

## Azure OpenAI
- Reuse a singleton `AsyncAzureOpenAI` client — never recreate per request.
- Handle `429` (Rate Limit) with retry-after header logic.
- Batch embedding calls — do not call the API per chunk individually.
- Always use the latest Azure OpenAI SDK.

## ChromaDB
- Use `PersistentClient(path="./chroma_db")` — not the in-memory client.
- ChromaDB returns L2 distance by default (lower = more similar).
- For cosine similarity: `metadata={"hnsw:space": "cosine"}` on collection creation.

## Logging
- Use structured JSON logging with custom formatter.
- Every request logs: `request_id`, `endpoint`, `tokens_used`, `latency_ms`, `prompt_version`, `guardrail_triggered`.

## Security
- Never hardcode secrets — use environment variables via `.env`.
- Sanitize user inputs before including in prompts.
- System prompts live in `backend/prompts/*.txt` — never hardcode prompt strings.
