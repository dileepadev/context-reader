---
name: deployment
description: "Set up Docker, CI/CD, environment files, and deployment verification for the ContextReader project. Use when scaffolding infrastructure, configuring docker-compose, writing GitHub Actions workflows, or creating .env.example files."
argument-hint: "Describe the deployment task (e.g., 'set up docker-compose for full stack')"
---

# Deployment

## When to Use

- Setting up Docker Compose for the full stack
- Writing or updating Dockerfiles for backend or frontend
- Creating or modifying GitHub Actions CI workflows
- Creating `.env.example` files for backend or frontend
- Verifying the deployment works end-to-end

## Docker Compose Setup

### Structure

```yaml
# docker-compose.yml (project root)
services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    env_file:
      - ./backend/.env
    volumes:
      - chroma_data:/app/chroma_db
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    env_file:
      - ./frontend/.env
    depends_on:
      backend:
        condition: service_healthy

volumes:
  chroma_data:
```

### Key Rules

- ChromaDB data MUST use a named volume or bind mount for persistence.
- Frontend depends on backend health check passing.
- Each service reads from its own `.env` file.
- Never copy `.env` files into Docker images.

## Backend Dockerfile

```dockerfile
# backend/Dockerfile
FROM python:3.11-slim AS base
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### .dockerignore (backend)

```
.env
__pycache__/
.pytest_cache/
chroma_db/
logs/
.venv/
```

## Frontend Dockerfile

```dockerfile
# frontend/Dockerfile
FROM node:18-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:18-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

EXPOSE 3000
CMD ["npm", "start"]
```

### .dockerignore (frontend)

```
.env
node_modules/
.next/
```

## Environment Files

### backend/.env.example

```env
# Azure OpenAI (required)
AZURE_OPENAI_API_KEY=your-api-key-here
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com/
AZURE_OPENAI_DEPLOYMENT_NAME=gpt-4o
AZURE_OPENAI_EMBEDDING_DEPLOYMENT=text-embedding-3-small
AZURE_OPENAI_API_VERSION=2024-02-01

# Application (optional)
CHROMA_PERSIST_DIR=./chroma_db
SIMILARITY_THRESHOLD=0.75
MAX_INPUT_LENGTH=1000
ACTIVE_PROMPT_VERSION=v2
LOG_LEVEL=INFO
```

### frontend/.env.example

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
```

## GitHub Actions CI

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  backend:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: backend
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
          cache: pip
      - run: pip install -r requirements.txt
      - run: python -m pytest tests/ -v

  frontend:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: frontend
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 18
          cache: npm
          cache-dependency-path: frontend/package-lock.json
      - run: npm ci
      - run: npm run lint
      - run: npx tsc --noEmit
```

## Verification Checklist

After deployment, confirm all of these pass:

```bash
# Backend health
curl -f http://localhost:8000/health

# Swagger docs
curl -f http://localhost:8000/docs

# Frontend loads
curl -f http://localhost:3000
```
