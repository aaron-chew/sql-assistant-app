# 21 — Prisma & Database Scaffolding

## Goal

Set up Prisma ORM with SQLite for local development so the database layer is ready when persistent features (e.g., user accounts, saved query history) are added post-MVP. This is scaffolding only — no features depend on it yet, but the requirements specify it as part of the stack and repo structure.

## What to Build

### Prisma Setup (`server/`)

- Install Prisma: `npm install prisma --save-dev` and `npm install @prisma/client`
- Initialize Prisma: `npx prisma init --datasource-provider sqlite`
- This creates `server/prisma/schema.prisma` and updates `.env` with `DATABASE_URL`

### Schema File (`server/prisma/schema.prisma`)

- Configure the datasource for SQLite (dev) with a note about switching to PostgreSQL for production
- Add a minimal placeholder model (e.g., `QueryLog`) to validate the setup works:
  ```prisma
  model QueryLog {
    id          String   @id @default(cuid())
    prompt      String
    sql         String
    createdAt   DateTime @default(now())
  }
  ```
- This model is not wired into any endpoint — it exists to confirm Prisma generates correctly

### Generate & Migrate

- Run `npx prisma migrate dev --name init` to create the initial migration and generate the Prisma client
- Confirm the SQLite database file is created at `server/prisma/dev.db`
- Add `server/prisma/dev.db` to `.gitignore`

### Environment Variables

- Confirm `DATABASE_URL=file:./dev.db` is in `server/.env` and `server/.env.example`

## Acceptance Criteria

- `server/prisma/schema.prisma` exists with a valid SQLite datasource and at least one model
- `npx prisma migrate dev` runs successfully and creates `dev.db`
- `npx prisma generate` creates the Prisma client without errors
- `dev.db` is excluded from version control via `.gitignore`
- `DATABASE_URL` is documented in `.env.example`

## References

- Requirements Section 5.3 (Database — SQLite dev, PostgreSQL prod, Prisma ORM)
- Requirements Section 6 (Repository structure — `server/prisma/schema.prisma`)
- Requirements Section 13 (Environment variables — `DATABASE_URL`)
- Requirements Section 17.4 (SQLite → PostgreSQL migration path)
