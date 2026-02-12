# 00 — App Skeleton & Repository Setup

## Goal

Set up the monorepo with a working React frontend and Express backend. Both should start independently, serve a basic page / response, and be wired with the correct folder structure from the requirements doc. This is the foundation everything else builds on.

## What to Build

### Repository Root

- `.gitignore` covering `node_modules/`, `.env`, `dist/`, build artifacts
- `.env.example` with all required environment variables (see Section 13 of requirements)
- Root-level structure matching Section 6 of the requirements

### Client (`client/`)

- Initialize with Vite + React (`npm create vite@latest client -- --template react`)
- Install Tailwind CSS and configure it (`tailwind.config.js`, `postcss.config.js`, `index.css` with `@tailwind` directives)
- `src/main.jsx` — renders `<App />` into the DOM
- `src/App.jsx` — renders a simple placeholder page (e.g., an `<h1>SQL Assistant</h1>`) to confirm the app works
- Create empty placeholder directories: `src/components/`, `src/pages/`, `src/services/`, `src/utils/`, `src/i18n/`
- Confirm: `npm run dev` starts the Vite dev server on port 5173

### Server (`server/`)

- `npm init -y`, then install Express and dotenv
- `src/index.js` — starts Express on port 3001 (read from `process.env.PORT`), loads `.env` via dotenv
- A single `GET /` route that returns `{ message: "SQL Assistant API is running" }` to confirm the server works
- Create empty placeholder directories matching Section 6: `src/routes/`, `src/controllers/`, `src/services/`, `src/middleware/`, `src/validators/`, `src/prompts/`, `src/errors/`, `src/utils/`, `src/config/`, `src/services/providers/`
- Confirm: `npm start` (or `node src/index.js`) starts the server on port 3001

### Graceful Shutdown

- Add `SIGTERM` handler in `server/src/index.js` so the server finishes in-flight requests before exiting (see Section 17.2)

## Acceptance Criteria

- Running `npm run dev` in `client/` opens a page in the browser showing "SQL Assistant"
- Running `node src/index.js` in `server/` starts an Express server; `curl http://localhost:3001/` returns the JSON message
- The folder structure matches the layout in Section 6 of the requirements (empty placeholder dirs are fine)
- `.env.example` exists at the project root with all environment variables listed
- `.gitignore` properly excludes `node_modules/`, `.env`, and build artifacts

## References

- Requirements Section 5.1 (Frontend stack)
- Requirements Section 5.2 (Backend stack)
- Requirements Section 6 (Repository structure)
- Requirements Section 13 (Environment variables)
- Requirements Section 17.2 (Graceful shutdown)
- Requirements Section 14, Steps 1
