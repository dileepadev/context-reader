## Project

`context-reader` is a documentation and governance template repository. It contains contribution guidelines, issue/PR templates, commit/branch/PR naming conventions, changelog, versioning strategy, and security policy. There is no application source code.

## Writing Standards

- Write in clear, concise English.
- Use ATX-style headings (`#`, `##`, `###`).
- Use reference-style links for URLs that appear more than once.
- Use tables for structured data; align columns with pipes.
- Use fenced code blocks with language identifiers (` ```bash `, ` ```md `).
- One sentence per line in source markdown for cleaner diffs.

## Conventions

- **Commit messages**: Follow [COMMIT_MESSAGE_GUIDELINES.md](../COMMIT_MESSAGE_GUIDELINES.md) — format: `<type>(<scope>): <short message>`.
- **Branch names**: Follow [BRANCH_NAMING_GUIDELINES.md](../BRANCH_NAMING_GUIDELINES.md) — format: `<type>/x` (e.g., `docs/readme`).
- **PR titles**: Follow [PULL_REQUEST_GUIDELINES.md](../PULL_REQUEST_GUIDELINES.md) — format: `<type>(<branch>): <message> [#issue]`.
- **Changelog**: Follow the categories in [CHANGELOG.md](../CHANGELOG.md) — Added, Changed, Fixed, Removed.
- **Versioning**: Follow [VERSIONING.md](../VERSIONING.md) — SemVer (`MAJOR.MINOR.PATCH`).
- **Issue templates**: Use the templates in [.github/ISSUE_TEMPLATE/](./ISSUE_TEMPLATE/).
- **PR template**: Use [.github/PULL_REQUEST_TEMPLATE.md](./PULL_REQUEST_TEMPLATE.md).

## Key Rules

- Do not duplicate content already in guideline docs — link to them instead.
- Keep all documentation consistent with the existing tone and structure.
- Update `CHANGELOG.md` when making notable changes.
- Report security vulnerabilities to `contact@dileepa.dev` per [SECURITY.md](../SECURITY.md).
