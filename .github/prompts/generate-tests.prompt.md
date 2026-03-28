---
description: "Generate tests for a backend module using pytest. Covers unit tests, edge cases, and mocking Azure OpenAI calls."
agent: "backend"
tools: ["read", "edit", "search", "execute"]
argument-hint: "Specify the module to test (e.g., 'retrieval.py guardrail logic')"
---

Your goal is to generate pytest tests for a backend module.

## Steps

1. Read the target module in `backend/` to understand its functions and logic.
2. Read existing tests in `backend/tests/` to match the test structure and patterns.
3. Create or update the test file at `backend/tests/test_<module>.py`.
4. Include:
   - Happy path tests
   - Edge cases (empty input, boundary values, malformed data)
   - Error handling (what happens when Azure OpenAI returns 429, ChromaDB is unreachable)
5. Mock external dependencies:
   - Use `unittest.mock.AsyncMock` for Azure OpenAI client calls.
   - Use `unittest.mock.patch` for ChromaDB operations.
   - Never make real API calls in tests.
6. Run tests: `cd backend && python -m pytest tests/ -v`
