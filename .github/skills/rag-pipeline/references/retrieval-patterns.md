# Retrieval Patterns

## Similarity Search

```python
results = collection.query(
    query_embeddings=[query_vector],
    n_results=5,
    include=["documents", "distances", "metadatas"]
)
chunks = results["documents"][0]
scores = results["distances"][0]
metadatas = results["metadatas"][0]
```

## Distance Metrics

| Space  | ChromaDB Returns   | Similar = | Default |
| ------ | ------------------ | --------- | ------- |
| L2     | Euclidean distance | Lower     | Yes     |
| cosine | Cosine distance    | Lower     | No      |
| ip     | Inner product      | Higher    | No      |

Set space on collection creation: `metadata={"hnsw:space": "cosine"}`.

## Guardrail Logic

```python
SIMILARITY_THRESHOLD = float(os.getenv("SIMILARITY_THRESHOLD", "0.75"))
best_score = min(scores)  # Lower = more similar for L2 and cosine

if best_score > SIMILARITY_THRESHOLD:
    logger.info("Guardrail triggered", extra={
        "query": query,
        "best_score": best_score,
        "threshold": SIMILARITY_THRESHOLD
    })
    return {
        "answer": "I don't have enough context in the uploaded document to answer that.",
        "guardrail_triggered": True,
        "sources": []
    }
```

## Building Context for Prompt

```python
context_parts = []
for i, (chunk, meta) in enumerate(zip(chunks, metadatas)):
    label = f"[Chunk {i+1}] (page {meta.get('page_number', '?')})"
    context_parts.append(f"{label}\n{chunk}")
context = "\n\n".join(context_parts)
```
