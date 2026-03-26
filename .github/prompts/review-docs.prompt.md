---
description: "Review all documentation files for quality, consistency, and adherence to project conventions."
agent: "reviewer"
tools: ["read", "search"]
argument-hint: "Optionally specify files or areas to focus on"
---

Review the documentation in this repository for quality and consistency.

## Focus Areas

1. **Formatting**: ATX-style headings, fenced code blocks with language IDs, properly aligned tables.
2. **Links**: All relative links resolve to actual files in the repository.
3. **Conventions**: Commit message, branch naming, and PR title formats match their respective guideline documents.
4. **Consistency**: Tone, structure, and terminology are uniform across all files.
5. **Completeness**: No placeholder sections left unfinished (except TODO.md).
6. **One sentence per line**: Source Markdown follows one sentence per line for clean diffs.

## Scope

Review all `.md` files in the repository root and `.github/` directory.
Report issues as a structured table with file, line, issue description, and severity.
