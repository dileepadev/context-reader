---
description: "Create a new GitHub issue template in .github/ISSUE_TEMPLATE/ following existing patterns."
agent: "agent"
tools: ["read", "edit", "search"]
argument-hint: "Describe the template purpose (e.g., 'performance issue report')"
---

Your goal is to create a new GitHub issue template.

## Steps

1. Read ALL existing templates in `.github/ISSUE_TEMPLATE/` to understand the established pattern.
2. Create the new template file in `.github/ISSUE_TEMPLATE/` with a descriptive kebab-case filename (e.g., `performance-issue.md`).
3. Include the YAML frontmatter:
   ```yaml
   ---
   name: "<Template Name>"
   about: "<Brief description>"
   title: ""
   labels: ""
   assignees: ""
   ---
   ```
4. Include the standard sections with matching emoji conventions:
   - `## 📜 Description`
   - Any template-specific sections appropriate for the purpose
   - `## 🔗 Related Issues/PRs`
   - `## 📝 Additional Information`
5. Use placeholder text that guides the user on what to write.
