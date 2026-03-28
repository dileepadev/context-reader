# Prompt Templates

## Versioning Strategy

- Prompts stored in `backend/prompts/` as plain `.txt` files.
- Active version controlled by `ACTIVE_PROMPT_VERSION` env var (default: `v2`).
- Every response logs which prompt version was used.
- Never hardcode prompt strings in Python code.

## Loading Prompts

```python
from pathlib import Path

def load_prompt(version: str = None) -> str:
    version = version or os.getenv("ACTIVE_PROMPT_VERSION", "v2")
    prompt_path = Path("backend/prompts") / f"{version}_system.txt"
    return prompt_path.read_text()
```

## v1 Baseline (`v1_system.txt`)

```text
You are a document assistant. Answer the user's question using ONLY the context provided below.
If the context does not contain the answer, say so explicitly.

Context:
{chunks}

Question: {question}
```

## v2 Improved (`v2_system.txt`)

```text
You are a document assistant. Answer the user's question using ONLY the context provided below.

Rules:
- Cite your sources using [Chunk N] notation
- If the context does not contain the answer, say so explicitly
- Do not use any knowledge outside the provided context
- Be concise and direct

Context:
{chunks}

Question: {question}
```

## Prompt Assembly

```python
system_prompt = load_prompt()
formatted = system_prompt.replace("{chunks}", context).replace("{question}", user_question)
messages = [
    {"role": "system", "content": formatted},
    {"role": "user", "content": user_question}
]
```
