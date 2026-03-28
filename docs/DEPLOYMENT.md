# Deployment

This guide covers all deployment options for ContextReader.

## Prerequisites

- [Python 3.11+](https://www.python.org/downloads/)
- [Node.js 18+](https://nodejs.org/)
- [Docker](https://docs.docker.com/get-docker/) and Docker Compose (for containerized deployment)
- An Azure OpenAI resource with the following deployments:
  - `GPT-4o` (chat completion)
  - `text-embedding-3-small` (embeddings)

## Environment Setup

### 1. Create Your Environment Files

Each sub-project has its own `.env.example`:

```bash
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
```

### 2. Configure Azure OpenAI Credentials

Edit `backend/.env`:

```env
# Required — Azure OpenAI
AZURE_OPENAI_API_KEY=your-api-key-here
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com/
AZURE_OPENAI_DEPLOYMENT_NAME=gpt-4o
AZURE_OPENAI_EMBEDDING_DEPLOYMENT=text-embedding-3-small
AZURE_OPENAI_API_VERSION=2024-02-01

# Optional — Application
CHROMA_PERSIST_DIR=./chroma_db
SIMILARITY_THRESHOLD=0.75
MAX_INPUT_LENGTH=1000
ACTIVE_PROMPT_VERSION=v1
LOG_LEVEL=INFO
```

> **Security:** Never commit `.env` to version control.
> Both `.gitignore` files already exclude it.

### 3. Azure OpenAI Resource Setup

If you do not yet have an Azure OpenAI resource:

1. Go to the [Azure Portal](https://portal.azure.com/)
2. Create an **Azure OpenAI** resource
3. Deploy the following models:
   - `gpt-4o` — for chat completion
   - `text-embedding-3-small` — for document embeddings
4. Copy the **endpoint** and **API key** from the resource's Keys and Endpoint page

## Docker Compose (Recommended)

The simplest way to run the full stack:

```bash
docker-compose up --build
```

This starts:

| Service | URL | Description |
| --- | --- | --- |
| Frontend | <http://localhost:3000> | Next.js chat interface |
| Backend | <http://localhost:8000> | FastAPI REST API |
| API Docs | <http://localhost:8000/docs> | Swagger UI |

To stop:

```bash
docker-compose down
```

To rebuild after code changes:

```bash
docker-compose up --build
```

### Persistent Data

ChromaDB data is stored in a Docker volume mapped to `./chroma_db`.
This directory persists between container restarts.

## Local Development (Without Docker)

### Backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate       # Linux/macOS
# .venv\Scripts\activate        # Windows
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

Verify the backend is running:

```bash
curl http://localhost:8000/health
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

The frontend runs at <http://localhost:3000> and connects to the backend at <http://localhost:8000>.

## Running Tests

### Backend Tests

```bash
cd backend
python -m pytest tests/ -v
```

### Frontend Lint and Type Check

```bash
cd frontend
npm run lint
npx tsc --noEmit
```

## Production Considerations

### Performance

- **Singleton client** — The `AsyncAzureOpenAI` client is created once at startup and reused for all requests.
- **Batch embeddings** — All chunks from a document are embedded in a single API call.
- **Streaming** — Responses are streamed token-by-token via SSE, reducing time-to-first-token.

### Security

- All credentials are loaded from environment variables — never hardcoded.
- Prompt injection detection runs on every user query before processing.
- Input length is capped via `MAX_INPUT_LENGTH`.
- CORS is configured to allow only the frontend origin.

### Monitoring

- Structured JSON logs are written to `backend/logs/` with:
  - `request_id` for tracing
  - `latency_ms` for performance monitoring
  - `tokens_used` for cost tracking
  - `guardrail_triggered` for safety monitoring
  - `prompt_version` for experiment tracking

### Scaling

- **Horizontal scaling** — Run multiple FastAPI workers behind a reverse proxy (nginx, Caddy).
- **Volume persistence** — Ensure the ChromaDB directory is mounted on persistent storage.
- **Rate limiting** — The backend handles Azure OpenAI `429` responses with retry-after logic.
