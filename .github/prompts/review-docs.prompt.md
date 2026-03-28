---
description: "Review code and documentation for quality, consistency, security, and adherence to project conventions."
agent: "reviewer"
tools: ["read", "search"]
argument-hint: "Optionally specify files or areas to focus on"
---

Review the ContextReader codebase and documentation for quality and consistency.

## Focus Areas

1. **Backend code**: Async endpoints, singleton client reuse, no hardcoded secrets, structured logging, input sanitization.
2. **Frontend code**: TypeScript strict mode, SSE streaming (not WebSockets), proper error handling.
3. **Security**: No secrets in code, CORS config, prompt injection detection, input validation.
4. **ChromaDB**: PersistentClient usage, batch embeddings, correct distance metric.
5. **Documentation**: Links resolve, conventions match guideline docs, no stale content.
6. **Tests**: Coverage for ingestion, retrieval guardrails, and sanitizer.

## Scope

Review all files in `backend/`, `frontend/`, and root `.md` files.
Report issues as a structured table with file, line, issue description, and severity.
