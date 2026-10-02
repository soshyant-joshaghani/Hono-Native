[![](./FoxG-Kit.png)](./FoxG-Kit.png)

# Hono-Native

GitHub: [Hono-Native](https://github.com/soshyant-joshaghani/Hono-Native)

**One Hono API. A Svelte site you install. Native shells later.**

The starter-tier multi-client Hono kit of the FoxG family. The backend in this repository is the Hono app (routes, services, repositories, Drizzle, a Redis list worker). It follows the [FoxG wire contract](../../../CONTRACT.md). It is not a frontend: `frontend/web` stays empty until the control CLI copies Hono-Svelte's frontend into it.

**Docs:** [AGENTS.md](AGENTS.md) · [ROADMAP.md](ROADMAP.md) · [docs/](docs/) · [plans](__plans__/PROGRESS.md) · [`__ctrl__`](__ctrl__/README.md)

```bat
__ctrl__\hono-native-ctrl.bat setup-local
__ctrl__\hono-native-ctrl.bat web list
__ctrl__\hono-native-ctrl.bat web use svelte
__ctrl__\hono-native-ctrl.bat dev run all
```

Linux and macOS use `__ctrl__/hono-native-ctrl.sh`. `setup-local` copies `.env.example` to `.env` when needed, runs `npm install` for the workspace (`backend`, `backend/contracts`) and builds `backend/contracts`. Prerequisites: Node.js 22, Python 3.12+ for `__ctrl__`, Docker.

`web use` reads `__ctrl__/kits.json`. The only kit today is `svelte` (`../hono-svelte/frontend`). Next and Nuxt are not listed until those kits exist.

| Command | Effect |
|---------|--------|
| `web list` | Show the catalog and which kit is installed |
| `web use svelte` | Copy that frontend into `frontend/web`, rewrite its Dockerfiles for `frontend/web` as the build context, and write `frontend/kit.lock.json` |
| `web use svelte --replace` | Copy again |
| `web use svelte --replace --force` | Replace local edits in `frontend/web` |
| `web status` | Show the lock and whether the tree changed |

The copied frontend calls `/api/v1` with plain `fetch` and imports nothing from `backend/`, so it runs against this API or any other FoxG backend.

Windows and Android folders under `frontend/` are reserved. They call the same API. Swagger UI is http://api.localhost/docs and Scalar is http://api.localhost/sdoc. Superuser `admin@example.com` / `Admin@1234`; change both before a shared deploy.

```text
backend/                    Hono API and worker (Node.js)
  contracts/                Zod schemas (snake_case wire shapes)
  drizzle/                  SQL migrations, IF NOT EXISTS, applied when the API starts
  src/modules/{apps,base,system}  Route → Service → Repository (Drizzle)
  src/core/ src/worker/     config, db, cache, jobs, security; Redis list worker
frontend/web                filled by web use svelte
frontend/win, android      reserved native clients
tests/                      backend/ (Vitest on PGlite) and contract/
```

## Tests

```bat
__ctrl__\hono-native-ctrl.bat test backend
__ctrl__\hono-native-ctrl.bat test contract --base http://localhost:8000
```

`test contract` runs the shared `contract_test.py` against a running API. See [docs/testing.md](docs/testing.md).

Changing backend works as in every FoxG kit: keep `frontend/`, the module names, and the contract, then reimplement `backend/`. Index: [foxg-kit](../../../README.md).
