# Agent Instructions

This file provides instructions for AI coding agents (GitHub Copilot agent mode, etc.) working in this repository.

## Repository Summary

`context-reader` is a documentation and governance template repository owned by [dileepadev](https://github.com/dileepadev).
It contains contribution guidelines, issue/PR templates, commit/branch/PR naming conventions, changelog, versioning strategy, and security policy.
There is **no application source code** — all files are Markdown documentation.

## Validated Commands

Before opening a pull request, verify all Markdown files are well-formed:

```bash
# Check for broken relative links (requires markdown-link-check)
find . -name '*.md' -not -path './.git/*' | xargs -I {} markdown-link-check {}
```

## Editing Documentation

- All files are Markdown (`.md`) in the repository root or `.github/`.
- Follow the writing standards in [.github/copilot-instructions.md](.github/copilot-instructions.md).
- One sentence per line in source Markdown for cleaner diffs.
- Use ATX-style headings, reference-style links, and fenced code blocks.

## Adding an Issue Template

- Create the file in `.github/ISSUE_TEMPLATE/`.
- Follow the structure of existing templates (YAML frontmatter with `name`, `about`, `title`, `labels`, `assignees`).
- Include standard sections: Description, Related Issues/PRs, Additional Information.

## Updating Changelog

- Add entries under the `[Unreleased]` section in `CHANGELOG.md`.
- Use the categories: **Added**, **Changed**, **Fixed**, **Removed**.

## Do Not

- Duplicate content already in guideline docs — link to them instead.
- Add application source code to this repository.
- Commit build artifacts or editor-specific files.
