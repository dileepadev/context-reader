---
name: rag-pipeline
description: "Build or modify the RAG pipeline: document ingestion, chunking, embedding, ChromaDB storage, similarity retrieval, guardrail logic, prompt assembly, and SSE streaming generation. Use when working on ingestion.py, retrieval.py, streaming.py, or the end-to-end pipeline."
argument-hint: "Describe the pipeline change (e.g., 'add metadata filtering to retrieval')"
---

# RAG Pipeline

## When to Use

- Building or modifying the ingestion pipeline (parsing, chunking, embedding)
- Implementing or tuning retrieval logic (similarity search, guardrails)
- Working on streaming generation (prompt assembly, SSE output)
- Debugging pipeline issues end-to-end

## Pipeline Overview

```
INGESTION: Upload → Parse → Chunk → Embed → Store in ChromaDB
QUERY:     Question → Embed → Search ChromaDB → Guardrail → Build Prompt → Stream GPT-4o → Citations
```

## Ingestion Procedure

1. **Parse file** — PyMuPDF for PDF, python-docx for DOCX, plain read for TXT.
2. **Chunk text** — Use `RecursiveCharacterTextSplitter`:
   ```python
   from langchain_text_splitters import RecursiveCharacterTextSplitter
   splitter = RecursiveCharacterTextSplitter(
       chunk_size=1000, chunk_overlap=200,
       separators=["\n\n", "\n", ". ", " ", ""]
   )
   ```
3. **Embed chunks** — Batch call to Azure OpenAI `text-embedding-3-small`. Never call per-chunk.
4. **Store** — ChromaDB `PersistentClient(path="./chroma_db")`, one collection per document. Include metadata: `filename`, `chunk_index`, `page_number`.

## Retrieval Procedure

1. **Embed query** — Same model as ingestion.
2. **Similarity search** — `collection.query(query_embeddings=[vec], n_results=5)`.
3. **Guardrail check** — ChromaDB returns L2 distance (lower = more similar):
   ```python
   SIMILARITY_THRESHOLD = float(os.getenv("SIMILARITY_THRESHOLD", "0.75"))
   if min(scores) > SIMILARITY_THRESHOLD:
       return {"answer": "I don't have enough context...", "guardrail_triggered": True}
   ```
4. **Alternative**: Use cosine space — `metadata={"hnsw:space": "cosine"}` on collection creation.

## Streaming Generation Procedure

1. **Load prompt** — Read from `backend/prompts/{ACTIVE_PROMPT_VERSION}_system.txt`.
2. **Assemble** — System prompt + retrieved chunks (with `[Chunk N]` labels) + user question.
3. **Stream** — Use `AsyncAzureOpenAI` with `stream=True`, yield SSE events.
4. **Track** — Log `prompt_tokens`, `completion_tokens`, estimated cost to `logs/cost.jsonl`.

## Key References

- [Ingestion module](./references/ingestion-patterns.md)
- [Retrieval patterns](./references/retrieval-patterns.md)
- [Prompt templates](./references/prompt-templates.md)
