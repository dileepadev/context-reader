---
description: "Use when updating CHANGELOG.md. Covers entry format, category rules, and link conventions for this project."
applyTo: "CHANGELOG.md"
---

# Changelog Conventions

## Entry Format

Add new entries under the `## [Unreleased]` section.
When releasing, move entries to a dated version heading: `## [vX.Y.Z] - YYYY-MM-DD`.

## Categories

Use only these categories in this order:

- **Added** — New features or documentation.
- **Changed** — Modifications to existing content.
- **Fixed** — Bug fixes or corrections.
- **Removed** — Deleted features or content.

## Rules

- Each entry is a bullet point starting with a capital letter.
- Do not end entries with a period.
- Reference issue/PR numbers where applicable: `(#123)`.
- Add a reference-style link at the bottom for each new version:
  `[vX.Y.Z]: https://github.com/dileepadev/context-reader/releases/tag/vX.Y.Z`
