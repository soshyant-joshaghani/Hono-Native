# Execution

Hono-Native follows Fast-Native's shape: one API, a switchable web slot, native shells that are not a browser around the site.

## Order

1. The API in `backend/` follows [CONTRACT.md](../../../../CONTRACT.md) and is the same Hono backend as Hono-Svelte.
2. `web use svelte` copies Hono-Svelte's frontend into `frontend/web`.
3. Build WinUI and Compose against the same `/api/v1` routes.

Do not add Hono-Next or Hono-Nuxt here.
