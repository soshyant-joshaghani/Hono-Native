# AGENTS.md — Hono-Native

Hono-Native is a multi-client kit around the Hono API. The API follows [CONTRACT.md](../../../CONTRACT.md). The reference frontend is Hono-Svelte's.

- Do not invent a second backend style here. Do not reshape the API as FastAPI, Elysia, or Go.
- Do not wrap the website in a WebView and call it the native app.
- `frontend/web` is filled by `web use`, not by hand-copying UI from Fast kits.
- `frontend/win` and `frontend/android` are not built yet. Add them as clients of `/api/v1`, using `frontend/client-routes.json` for screen names.
- Request and response shapes are the wire format in [CONTRACT.md](../../../CONTRACT.md): snake_case JSON, `{"detail": ...}` errors, form login. The Zod schemas in `backend/contracts` implement it for the backend; clients do not import them.
- Keep `__plans__/` in git.

Backend layout: `backend/src/modules/{apps,base,system}`, `backend/src/core`, `backend/src/worker`. Layers: route → service → repository → Drizzle → PostgreSQL. Services throw `ApiError`; the response is always `{"detail": "..."}`. Migrations are SQL in `backend/drizzle` written `IF NOT EXISTS`, applied when the API starts. Jobs use the Redis list protocol in the contract (`foxg:jobs`). Do not add a queue library.

Run `__ctrl__\hono-native-ctrl.bat test backend` before calling a stage done, and `test contract` against a running API when routes or shapes change.
