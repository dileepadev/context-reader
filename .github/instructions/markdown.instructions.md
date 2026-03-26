---
description: "Use when writing or editing Markdown files. Covers formatting standards, heading conventions, link styles, and code block usage for this project."
applyTo: "**/*.md"
---

# Markdown Standards

- Use ATX-style headings (`#`, `##`, `###`) — not Setext (underlines).
- One sentence per line in source Markdown for cleaner diffs.
- Use reference-style links (`[text][ref]`) for URLs that appear more than once in a file.
- Use fenced code blocks with language identifiers (` ```bash `, ` ```md `).
- Use tables for structured data; align columns with pipes.
- Do not use HTML tags when Markdown syntax is sufficient.
- Separate headings from surrounding content with blank lines.
- Do not skip heading levels (e.g., `##` followed by `####`).
