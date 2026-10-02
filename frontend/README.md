# Web slot

`frontend/web` is empty until:

```bat
__ctrl__\hono-native-ctrl.bat web use svelte
```

That copies the Hono-Svelte `frontend/` directory here and rewrites its Dockerfiles for `frontend/web` as the build context. The copy calls `/api/v1` with plain `fetch` and imports nothing from `backend/`, so it runs against this API or any other FoxG backend.

`win` and `android` are reserved for native clients. `mac`, `linux`, and `ios` are reserved and not built.
