---
description: "Use when writing or editing TypeScript frontend code. Covers Next.js 16 App Router patterns, SSE streaming, Tailwind CSS, and component conventions."
applyTo: "frontend/**/*.{ts,tsx}"
---

# TypeScript Frontend Standards

## Next.js

- Use Next.js 16 App Router (`app/` directory).
- TypeScript strict mode — no `any` types.
- Pages go in `frontend/app/`, components in `frontend/components/`.

## SSE Streaming

- Use `ReadableStream` + `fetch` + `getReader()` for SSE streaming.
- Do NOT use WebSockets for streaming responses.
- API client logic lives in `frontend/lib/api.ts`.

## Styling

- Use Tailwind CSS for all styling.
- No CSS modules, styled-components, or external UI libraries unless explicitly requested.

## Components

- Functional components with React hooks.
- Keep components focused — one responsibility per file.
- Key components: `ChatWindow`, `FileUpload`, `SourceCitation`, `TokenUsage`.

## State and Error Handling

- Every async operation must show loading, success, and error states.
- Display user-friendly messages for network errors and API failures.
- Use `AbortController` to cancel in-flight requests when the user navigates away.

## Environment Variables

- Backend URL comes from `NEXT_PUBLIC_API_URL` — never hardcode `localhost`.
- Store all frontend env vars in `frontend/.env.example` with placeholder values.
- Access via `process.env.NEXT_PUBLIC_*` only.
