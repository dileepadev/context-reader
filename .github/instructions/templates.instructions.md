---
description: "Use when creating or editing GitHub issue templates. Covers YAML frontmatter structure, required sections, and emoji conventions."
applyTo: ".github/ISSUE_TEMPLATE/**"
---

# Issue Template Conventions

## YAML Frontmatter

Every template must start with:

```yaml
---
name: "<Template Name>"
about: "<Brief description of when to use this template>"
title: ""
labels: ""
assignees: ""
---
```

## Required Sections

All templates must include these sections using the established emoji headings:

- `## 📜 Description` — What the issue is about.
- `## 🔗 Related Issues/PRs` — Cross-references (use `- #<number>` format).
- `## 📝 Additional Information` — Supplementary context.

## Guidelines

- Match the tone, structure, and emoji usage of existing templates.
- Use checkboxes (`- [ ]`) for actionable lists (e.g., objectives, environment details).
- Keep placeholder text instructive (e.g., "A clear and concise description of...").
