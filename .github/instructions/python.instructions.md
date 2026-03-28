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
- Include a `/health` endpoint that verifies ChromaDB and Azure OpenAI connectivity.

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
- Write logs to `backend/logs/` directory.

## Cost Tracking

- Log `prompt_tokens`, `completion_tokens`, and estimated cost per request.
- Write cost entries to `backend/logs/cost.jsonl`.
- Use model-specific pricing constants — do not hardcode dollar values inline.

## Error Handling

- Catch `openai.RateLimitError` and retry with the `retry-after` header value.
- Catch `openai.APIError` and return a structured JSON error with `request_id`.
- Return `422` for invalid file types or input validation failures.
- Return `503` when ChromaDB or Azure OpenAI is unreachable.

## Testing

- Tests go in `backend/tests/test_<module>.py`.
- Mock external services (`AsyncAzureOpenAI`, `ChromaDB`) in unit tests.
- Use `pytest` with `pytest-asyncio` for async test functions.
- Test guardrail edge cases: threshold boundary, empty results, all-filtered results.

## Security

- Never hardcode secrets — use environment variables via `.env`.
- Sanitize user inputs before including in prompts.
- Enforce `MAX_INPUT_LENGTH` on all user-facing text inputs.
- Configure CORS to allow only the frontend origin.
- System prompts live in `backend/prompts/*.txt` — never hardcode prompt strings.
