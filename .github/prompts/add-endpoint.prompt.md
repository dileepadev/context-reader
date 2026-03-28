---
description: "Add a new FastAPI endpoint to the backend with proper async pattern, logging, and error handling."
agent: "backend"
tools: ["read", "edit", "search", "execute"]
argument-hint: "Describe the endpoint (e.g., 'GET /metrics returning request stats')"
---

Your goal is to add a new FastAPI endpoint to `backend/main.py`.

## Steps

1. Read `backend/main.py` to understand the existing route structure and imports.
2. Read `.github/copilot-instructions.md` for backend conventions.
3. Create the new endpoint following these rules:
   - Use `async def` for the handler.
   - Add structured JSON logging with `request_id`, `endpoint`, `latency_ms`.
   - Return proper HTTP status codes and JSON responses.
   - Handle errors gracefully with appropriate status codes.
   - Add CORS if the endpoint is called from the frontend.
4. If the endpoint needs new logic, create it in the appropriate module (`ingestion.py`, `retrieval.py`, `streaming.py`) — not inline in `main.py`.
5. Add a test in `backend/tests/` for the new endpoint.
