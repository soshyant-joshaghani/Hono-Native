# hono-native `__ctrl__`

The **control layer** for Hono-Native — one CLI for the web kit, native shells, and the project lifecycle.

```bat
hono-native-ctrl.bat web use svelte
hono-native-ctrl.bat setup-local
hono-native-ctrl.bat dev run all
hono-native-ctrl.bat native run win
hono-native-ctrl.bat native run android
```

## Command map

| Area | Commands |
|------|----------|
| Web kit | `web list` · `web use {svelte\|next\|nuxt\|rio}` · `web status` · `web update` |
| Native clients | `native list` · `native build {android\|win\|all}` · `native run {android\|win}` · `native clean {android\|win\|all}` |
| Local tooling | `setup-local [--force]` |
| Dev stack | `dev run\|stop\|down\|purge\|reset {infra,apps,all}` · `--slim` for lightweight runtime |
| App scaffold | `app create <name>` |
| Tests | `test {all,backend,frontend,contract}` |
| Local prod smoke | `prod start\|stop\|reset\|backup-acme\|…` |
| SSH / VM | `setup`, `pubkey`, `clone`, `env`, `start`, `stop`, `update`, `reset`, `backup-acme`, `connect`, … |

`web use` downloads that kit's `frontend/` from GitHub into `frontend/web` and writes `frontend/kit.lock.json`. `setup-local`, `dev run`, and `test frontend` stop until a kit is installed. Switching kits needs `web use <kit> --replace`. `web update` re-fetches the locked branch and stops when `frontend/web` has local edits unless you pass `--force`.

## Layout

| Path | Role |
|------|------|
| `kits.json` | Svelte, Next, Nuxt, and Rio download profiles |
| `platforms.json` | Windows and Android build paths |
| `servers.json` | Single VM entry |
| `safe/` | PEM, address, prod `.env` |
| `static/gpg` | Docker Ubuntu GPG (Iran bootstrap) |
| `remote/` | On-VM / local-prod compose scripts |
| `hono-native-ctrl.bat` / `.sh` | CLI entry |

## Local dev

`dev run` starts the backend on :8000 and the installed web kit on :5000. Svelte and Next use `npm run dev -w frontend`. Nuxt runs inside `frontend/web`. Rio runs `python -m rio run --port 5000 --public`.

Production compose builds `frontend/web` (`context: frontend/web`). `web use` rewrites the extracted Dockerfiles for that context.

## Native clients

```bat
hono-native-ctrl.bat native list
hono-native-ctrl.bat native build win
hono-native-ctrl.bat native run android
```

Windows is `dotnet build` of `FastNative.Client` without an MSIX package, then launch of the exe. Android is `gradlew assembleDebug`, then install and launch when adb sees a device.

## Quick start (Windows)

From `hono-native/__ctrl__/`:

```bat
hono-native-ctrl.bat
```

Interactive prompt, or one-shot:

```bat
hono-native-ctrl.bat setup-local
hono-native-ctrl.bat dev run all
hono-native-ctrl.bat test all
hono-native-ctrl.bat list
hono-native-ctrl.bat connect
```

Linux/mac:

```bash
chmod +x hono-native-ctrl.sh
./hono-native-ctrl.sh status
```

## Command map

| Area | Commands |
|------|----------|
| Local tooling | `setup-local [--force]` |
| Dev stack | `dev run\|stop\|down\|purge\|reset {infra,apps,all}` · `--slim` for lightweight runtime |
| App scaffold | `app create <name>` |
| Tests | `test {all,backend,frontend,contract}` |
| Local prod smoke | `prod start\|stop\|reset\|backup-acme\|…` |
| SSH / VM | `setup`, `pubkey`, `clone`, `env`, `start`, `stop`, `update`, `reset`, `backup-acme`, `connect`, … |

On-VM bash/bat scripts (what SSH `start`/`stop` invoke) live in [`remote/`](remote/README.md).

## Layout

| Path | Role |
|------|------|
| `servers.json` | Single VM entry (`fast-svelte`) |
| `safe/` | PEM, address, prod `.env` |
| `static/gpg` | Docker Ubuntu GPG (Iran bootstrap) |
| `remote/` | On-VM / local-prod compose scripts |
| `hono-native-ctrl.bat` / `.sh` | CLI entry |

## Typical first deploy (SSH)

```bat
hono-native-ctrl.bat setup
hono-native-ctrl.bat pubkey
REM add VM pubkey to GitHub
hono-native-ctrl.bat clone
hono-native-ctrl.bat env
hono-native-ctrl.bat start
```

Day-2:

```bat
hono-native-ctrl.bat update
hono-native-ctrl.bat status
hono-native-ctrl.bat backup-acme
```

## Local dev (Docker Desktop / host apps)

```bat
hono-native-ctrl.bat setup-local
hono-native-ctrl.bat dev run all
hono-native-ctrl.bat dev stop all
hono-native-ctrl.bat dev down all
hono-native-ctrl.bat dev purge infra
hono-native-ctrl.bat dev reset all
```

| Action | Infra (compose.dev.yml) | Apps (host) |
|--------|-------------------------|-------------|
| `run` / `start` | `up -d` db, redis (full), proxy, adminer | Hono API :8000 (runs the Drizzle SQL migrations), Redis queue worker (full), installed web kit :5000 |
| `stop` | `compose stop` — containers kept | kill host processes |
| `down` | `compose down` — volumes kept | kill host processes |
| `purge` | `compose down -v` — wipe data, stay down | kill host processes |
| `reset` | wipe then `run` | stop then run |

| Target | Notes |
|--------|-------|
| `infra` | Docker only. SQL migrations run when the API starts |
| `apps` | host processes (needs infra already up) |
| `all` | run: infra→apps · stop/down/purge/reset: apps→infra |

Opens browser tabs for Adminer / Traefik / dashboard / API docs after a successful run.

**Runtime profiles:** `dev run all` (full — includes Redis + worker) · `dev run all --slim` (no Redis/worker). See [docs/runtime-profiles.md](../docs/runtime-profiles.md).

## Tests

```bat
hono-native-ctrl.bat test all
hono-native-ctrl.bat test backend
hono-native-ctrl.bat test frontend
```

Backend tests run on an in-memory Postgres (PGlite) and need neither Postgres nor Redis. `test frontend` runs `svelte-check` inside `frontend/web` when a web kit is installed. `test contract [--base URL]` runs `tests/contract/contract_test.py` against a running API (default `http://localhost:8000`, `--local --jobs`).

## Local production smoke

```bat
hono-native-ctrl.bat prod start
hono-native-ctrl.bat prod stop
hono-native-ctrl.bat prod reset
hono-native-ctrl.bat prod backup-acme
```

Same scripts SSH uses under `remote/`. Prefer SSH `start`/`stop` when operating the real VM from your laptop.

## Setup (ctrl tool itself)

```bat
python -m venv .venv
.venv\Scripts\pip install -r requirements.txt
```

`setup-local` also runs `npm install` for the npm workspace (`backend`, `backend/contracts`) and builds `backend/contracts`.

On first `setup-local` / `dev run all`, the ctrl entry installs system **Python 3.10+** (via winget / Homebrew / apt) if missing, then `_setup_local` installs **Node.js LTS + npm** the same way before creating the project `.venv` and running `npm install`.

Iran VMs (`iran_setup: true`) keep provider DNS, rewrite apt to Arvan `apt_mirror`, and use Arvan Docker `registry_mirror`. `clone` routes GitHub SSH via `ssh.github.com:443`.

## Logs

```bat
REM Production VM (SSH)
hono-native-ctrl.bat logs api
hono-native-ctrl.bat logs db --no-follow

REM Local development
hono-native-ctrl.bat dev logs api
hono-native-ctrl.bat dev logs db

REM Local compose.yml smoke
hono-native-ctrl.bat prod logs api
```

## Flatten / restore-flat

```bat
hono-native-ctrl.bat flatten --yes
hono-native-ctrl.bat restore-flat
hono-native-ctrl.bat restore-flat --server <id> --yes
```

