<!-- Copilot instructions for AI coding agents working on this repo -->
# Repo-specific instructions for GitHub Copilot

Purpose
- Help contributors and AI agents make small, safe changes in this Next.js App Router dashboard.

Big picture
- This is a Next.js App Router app (app/). UI components live under `app/ui/` and top-level pages/layouts live under `app/layout.tsx`.
- Server-side logic and DB helpers are under `app/lib/`:
  - `app/lib/data.ts` contains the SQL queries and data-shaping helpers.
  - `app/lib/definitions.ts` defines shared TypeScript types.
  - `app/lib/utils.ts` contains pure helpers (date/currency/pagination).
- Database access uses the `postgres` client and `process.env.POSTGRES_URL` with `{ ssl: 'require' }`.
- Two example routes show server routes:
  - `app/seed/route.ts` seeds the database (creates tables and inserts placeholder data).
  - `app/query/route.ts` is a commented template for adding server GET handlers.

Developer workflows (commands)
- Install: `pnpm install` (this repo uses pnpm; a `pnpm-lock.yaml` is present).
- Dev: `pnpm dev` (runs `next dev --turbopack` per `package.json`).
- Build: `pnpm build` -> `next build`.
- Start: `pnpm start` -> `next start`.
- Local seed: run dev and call the seed route in a browser or with curl: `GET /seed` (e.g. `curl http://localhost:3000/seed`).

Important patterns & conventions
- Server-first data functions: prefer adding DB access in `app/lib/data.ts` and export small focused functions (e.g., `fetchLatestInvoices`, `fetchCardData`). Reuse these from server components or route handlers.
- Keep formatting/shape in `definitions.ts`. When transforming DB numbers to display, convert cents → dollars in data functions (see `fetchInvoiceById`).
- UI folder is the single place for presentational components; follow existing naming (e.g., `customers/table.tsx`, `invoices/table.tsx`).
- Routes that touch the DB run on the server. Avoid importing browser-only modules into `app/lib/*`.

Integration points & gotchas
- Environment: `POSTGRES_URL` must be set for DB queries. `app/lib/*` expects a live Postgres instance; `seed` will create tables if missing.
- Security: `app/seed/route.ts` is destructive/privileged — do not run on production without understanding.
- Native modules: `bcrypt` is used for seeding users; ensure native bindings are available on the host when running seed operations.
- Next.js config is minimal (`next.config.ts` is empty) — refer to `package.json` for script flags (turbopack in dev).

How to make safe PRs
- Small focused changes: update a single component or a data function and include a brief regression test plan (how to exercise the change in the browser).
- For DB changes, prefer a migration or use `app/seed/route.ts` as a reproducible seeding path for local testing.
- When changing shared types, update `app/lib/definitions.ts` and any affected callers in `app/lib/data.ts` and server components.

Examples (quick references)
- DB helper: change `fetchFilteredInvoices` in `app/lib/data.ts` to tune search behavior.
- Add a new server route: copy pattern from `app/query/route.ts` and use exported helpers from `app/lib/data.ts`.
- Seed locally: `pnpm dev` then `curl http://localhost:3000/seed`.

If unsure
- Inspect `app/lib/*` first for data flow, and `app/ui/*` for presentational patterns.
- Prefer minimal, localized changes. Ask for clarification rather than guessing database or auth behavior.

Please review and tell me if you'd like more detail on any area.
