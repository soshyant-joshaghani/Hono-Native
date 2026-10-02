# Tests

```bat
__ctrl__\hono-native-ctrl.bat test all
__ctrl__\hono-native-ctrl.bat test backend
__ctrl__\hono-native-ctrl.bat test frontend
__ctrl__\hono-native-ctrl.bat test contract --base http://localhost:8000
```

`test backend` runs Vitest (`npm run test -w @hono-svelte/backend`) on an in-memory Postgres (PGlite), so it needs no database and no Redis. Specs live in `tests/backend/`. `test frontend` runs `svelte-check` inside `frontend/web` when a web kit is installed. `test contract` runs `tests/contract/contract_test.py`, the wire-contract test every FoxG kit shares, against a running API. See [docs/testing.md](../docs/testing.md).
