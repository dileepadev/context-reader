---
name: testing
description: "Write or update tests for the ContextReader project. Covers pytest for Python backend (ingestion, retrieval, sanitizer) and lint/type-check for TypeScript frontend. Use when adding tests, fixing test failures, or improving coverage."
argument-hint: "Describe what to test (e.g., 'add tests for guardrail threshold logic')"
---

# Testing

## When to Use

- Writing new unit tests for backend modules
- Fixing failing tests
- Adding test coverage for edge cases
- Running the full test suite before a PR

## Backend Tests (pytest)

### Run

```bash
cd backend && python -m pytest tests/ -v
```

### Test Files

| Module            | Test File                    | What to Test                                                          |
| ----------------- | ---------------------------- | --------------------------------------------------------------------- |
| `ingestion.py`    | `tests/test_ingestion.py`    | Chunking boundaries, overlap, empty docs, file type handling          |
| `retrieval.py`    | `tests/test_retrieval.py`    | Guardrail triggers below threshold, passes above, score ordering      |
| `sanitizer.py`    | `tests/test_sanitizer.py`    | Injection patterns blocked, clean inputs pass, max length enforcement |
| `cost_tracker.py` | `tests/test_cost_tracker.py` | Token counting accuracy, cost calculation, JSONL output format        |
| `streaming.py`    | `tests/test_streaming.py`    | SSE event format, error propagation, prompt assembly                  |

### Patterns

```python
import pytest
from unittest.mock import AsyncMock, patch

# Test guardrail triggers
def test_guardrail_triggers_below_threshold():
    """Guardrail should return fallback when best score exceeds threshold."""
    scores = [0.9, 1.1, 1.3, 1.5, 1.7]  # All above 0.75
    assert min(scores) > 0.75

# Test chunking
def test_chunking_respects_size_limit():
    """No chunk should exceed chunk_size."""
    for chunk in chunks:
        assert len(chunk) <= 1000

# Mock Azure OpenAI calls
@patch("ingestion.client.embeddings.create", new_callable=AsyncMock)
async def test_embedding_batch_call(mock_embed):
    """Embeddings should be called once with all chunks, not per-chunk."""
    mock_embed.return_value = mock_response
    await embed_chunks(chunks)
    mock_embed.assert_called_once()
```

### Key Test Cases

- Chunking splits at correct boundaries with overlap
- Chunking handles empty document gracefully
- Chunking respects the 1000-character size limit on every chunk
- Guardrail triggers when best score exceeds threshold
- Guardrail passes when best score is within threshold
- Guardrail returns fallback message when all chunks are filtered
- Sanitizer blocks common injection patterns (`ignore previous instructions`, etc.)
- Sanitizer passes clean input without false positives
- Sanitizer enforces max input length
- Cost tracker calculates correct token counts from API response
- Cost tracker writes entries to `logs/cost.jsonl`
- Streaming endpoint returns SSE-formatted events
- Streaming endpoint handles Azure OpenAI errors gracefully

## Frontend Checks

### Run

```bash
cd frontend && npm run lint && npx tsc --noEmit
```

No unit test framework is required initially — lint and type-check are the minimum bar.
