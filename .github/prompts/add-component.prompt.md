---
description: "Add a new React component to the Next.js frontend with TypeScript and Tailwind CSS."
agent: "frontend"
tools: ["read", "edit", "search", "execute"]
argument-hint: "Describe the component (e.g., 'error banner that shows API failures')"
---

Your goal is to add a new React component to `frontend/components/`.

## Steps

1. Read existing components in `frontend/components/` to match patterns and style.
2. Create the component file in `frontend/components/` using PascalCase naming.
3. Follow these rules:
   - Functional component with TypeScript strict mode.
   - Props interface defined and exported.
   - Tailwind CSS for all styling — no CSS modules.
   - No `any` types.
4. If the component needs API calls, use the client from `frontend/lib/api.ts`.
5. For SSE streaming, use `ReadableStream` + `fetch` + `getReader()` — not WebSockets.
6. Import and use the component in the appropriate page (`frontend/app/page.tsx`).
