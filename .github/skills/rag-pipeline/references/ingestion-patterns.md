# Ingestion Patterns

## File Parsing

```python
import fitz  # PyMuPDF
from docx import Document

def extract_text(file_path: str, file_type: str) -> str:
    if file_type == "pdf":
        doc = fitz.open(file_path)
        return "\n".join(page.get_text() for page in doc)
    elif file_type == "docx":
        doc = Document(file_path)
        return "\n".join(p.text for p in doc.paragraphs)
    elif file_type == "txt":
        with open(file_path, "r") as f:
            return f.read()
    raise ValueError(f"Unsupported file type: {file_type}")
```

## Chunking

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    separators=["\n\n", "\n", ". ", " ", ""]
)
chunks = splitter.split_text(raw_text)
```

- `chunk_overlap=200` prevents answers being cut off at chunk boundaries.
- Separator hierarchy: paragraph → sentence → word — preserves semantic units.

## Batch Embedding

```python
# CORRECT — batch call
response = await client.embeddings.create(
    input=chunks,
    model=settings.AZURE_OPENAI_EMBEDDING_DEPLOYMENT
)
vectors = [item.embedding for item in response.data]

# WRONG — per-chunk call (slow, rate-limited)
# for chunk in chunks:
#     response = await client.embeddings.create(input=[chunk], ...)
```

## ChromaDB Storage

```python
import chromadb

client = chromadb.PersistentClient(path="./chroma_db")
collection = client.get_or_create_collection(
    name=document_id,
    metadata={"hnsw:space": "cosine"}  # Optional: cosine instead of L2
)
collection.upsert(
    ids=[f"{document_id}_chunk_{i}" for i in range(len(chunks))],
    documents=chunks,
    embeddings=vectors,
    metadatas=[{"filename": filename, "chunk_index": i, "page_number": page} for i, page in enumerate(pages)]
)
```
