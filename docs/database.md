# Database

PostgreSQL 18. Schema is `backend/src/db/schema.ts`. Migrations are SQL files in `backend/drizzle/`, applied by the Drizzle migrator when the API starts. `npm run db:migrate -w @hono-svelte/backend` applies the same folder on its own. The API then creates `FIRST_SUPERUSER` with `FIRST_SUPERUSER_PASSWORD` when that email is missing.

Tables in the canonical kit: `user` and `note`. They are the tables Fast's Alembic creates ([CONTRACT.md](../../../../CONTRACT.md), Data): same names, column types, nullability, `ix_user_email` (unique) and `ix_note_owner_id`. Ids and timestamps are set by the API, so the columns have no database defaults. `0000_init.sql` is written `IF NOT EXISTS`, so the API can start against a database that Fast, Rust, or .NET already built. A database created by an older Hono-Svelte (with `created_at` on `user`, `ON DELETE cascade`) is not migrated; recreate it.

Product tables belong to the app module that owns them. Add the Drizzle table, add a new `NNNN_name.sql` (write it `IF NOT EXISTS`) with its entry in `drizzle/meta/_journal.json`, and keep the repository as the only caller of that table.
