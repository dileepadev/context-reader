---
description: "Use when writing or editing Docker, docker-compose, CI/CD, or deployment configuration. Covers container patterns, GitHub Actions workflow, and environment setup."
applyTo: Dockerfile, docker-compose.yml, .github/workflows/**
---

# DevOps Standards

## Docker

- Separate Dockerfiles for backend (`backend/Dockerfile`) and frontend (`frontend/Dockerfile`).
- Use multi-stage builds to minimize image size.
- Do not copy `.env`, `__pycache__/`, `node_modules/`, `chroma_db/` into images.
- `docker-compose.yml` must start the full stack with one command.
- Map ChromaDB storage to a named volume or bind mount for persistence across restarts.

## GitHub Actions CI

- Pipeline: ESLint + Prettier → pytest → docker build.
- Run on every push and pull request to `main`.
- Fail fast on lint or test failures.
- Cache `pip` and `npm` dependencies between runs.

## Environment

- All secrets via environment variables — never hardcode.
- Provide `backend/.env.example` and `frontend/.env.example` with all required variables (no real values).
- Health check: `/health` endpoint verifies ChromaDB + Azure OpenAI connectivity.

## Verification

- After `docker-compose up --build`, confirm:
  - `curl http://localhost:8000/health` returns `200`.
  - Frontend loads at `http://localhost:3000`.
  - Swagger docs available at `http://localhost:8000/docs`.
- CI must run `python -m pytest tests/ -v` and `npm run lint && npx tsc --noEmit` before merging.
