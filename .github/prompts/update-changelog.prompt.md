---
description: "Add a new entry to the CHANGELOG.md under the Unreleased section."
agent: "agent"
tools: ["read", "edit", "search"]
argument-hint: "Describe the change to log (e.g., 'Added new security policy section')"
---

Your goal is to add a new entry to `CHANGELOG.md` under the `[Unreleased]` section.

## Steps

1. Read the current `CHANGELOG.md` to understand the existing structure.
2. Determine the correct category for the change: **Added**, **Changed**, **Fixed**, or **Removed**.
3. Add the entry as a bullet point under the appropriate category heading within `[Unreleased]`.
4. If the category heading does not exist yet under `[Unreleased]`, create it following the order: Added, Changed, Fixed, Removed.
5. Each entry starts with a capital letter and does not end with a period.
6. Reference issue or PR numbers where applicable: `(#123)`.
