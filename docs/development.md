# Development

From `hono-native/`, with Node.js 22+ and Docker. Install the Svelte site with `web use svelte` when `frontend/web` is empty.

```bat
__ctrl__\hono-native-ctrl.bat setup-local
__ctrl__\hono-native-ctrl.bat web use svelte
__ctrl__\hono-native-ctrl.bat dev run all
```

`setup-local` copies `.env.example` to `.env` when that file is missing, runs `npm install` for the workspace, and builds `backend/contracts`.

| Profile | Command | What starts |
|---------|---------|-------------|
| Full | `dev run all` | Postgres, Redis, Traefik, Adminer, API, Redis queue worker, Vite |
| Slim | `dev run all --slim` | Postgres, Traefik, Adminer, API, Vite |
| Infra | `dev run infra` | Containers |
| Apps | `dev run apps` | API, worker, Vite (infra already up) |

The API applies the SQL migrations when it starts. Stop with `dev stop all`.

| Surface | URL |
|---------|-----|
| Dashboard | http://dashboard.localhost |
| Sample notes | http://dashboard.localhost/sample/notes |
| Swagger | http://api.localhost/docs |
| Scalar | http://api.localhost/sdoc |
| Direct Vite | http://localhost:5000 |
| Direct API | http://localhost:8000/docs |
| Adminer | http://adminer.localhost |

Only one Traefik stack can use port 80. Host processes write logs under `__ctrl__/.run/` (gitignored).

Local superuser: `admin@example.com` / `Admin@1234` from `.env`. Change both before a shared deploy.
