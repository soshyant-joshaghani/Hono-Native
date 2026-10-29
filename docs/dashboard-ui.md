# Dashboard UI

The Svelte dashboard is Tailwind and shadcn-svelte. It is installed into `frontend/web` with `web use svelte`. The canonical page is sample notes at http://dashboard.localhost/sample/notes.

| Piece | Path |
|-------|------|
| Shell | `frontend/web/src/lib/modules/base/` |
| Primitives | `frontend/web/src/lib/modules/base/ui/` |
| Sample client | `frontend/web/src/lib/modules/apps/sample/` |
| Notes page | `frontend/web/src/routes/(dashboard)/sample/notes/+page.svelte` |
| Theme | `frontend/web/src/app.css` |

The installed frontend is a copy of Hono-Svelte's, which is the one Fast-Svelte ships. It calls the API with plain `fetch` (`$lib/config/backend` holds the base URL) and imports nothing from `backend/`, so the same UI runs against any FoxG backend. Keep the route and the module name when a product moves between families.
