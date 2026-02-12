# 20 — Deployment

## Goal

Deploy the frontend to Vercel and the backend to Render. Confirm the production app works end-to-end: frontend calls backend, backend calls LLM, response displays in the browser.

## What to Build

### Frontend Deployment (Vercel)

- Connect the `client/` directory to Vercel via Git-based deploy
- Set the following environment variables in Vercel's dashboard:
  - `VITE_API_URL` — the Render backend URL (e.g., `https://sql-assistant-api.onrender.com`)
- Confirm the build command is `npm run build` and the output directory is `dist/`
- Verify the deployed frontend loads and renders correctly on the `.vercel.app` URL

### Backend Deployment (Render)

- Create a new Web Service on Render, connected to the repo's `server/` directory
- Set the following environment variables in Render's dashboard:
  - `NODE_ENV=production`
  - `PORT` (Render sets this automatically, but confirm)
  - `ALLOWED_ORIGIN` — the Vercel frontend URL (e.g., `https://sql-assistant.vercel.app`)
  - `ANTHROPIC_API_KEY` — production API key (separate from dev key)
  - `OPENAI_API_KEY` — optional fallback provider key (also a separate production key)
  - `LLM_PRIMARY_PROVIDER=anthropic`
  - `LOG_LEVEL=info`
- Configure Render to use the health check endpoint: `GET /api/health` every 30 seconds
- Confirm the deployed backend responds to health checks

### Production Verification

- Verify CORS: the frontend on Vercel can call the backend on Render without CORS errors
- Verify the full flow: type a prompt in the frontend → backend receives it → LLM returns SQL → frontend displays the result
- Verify error handling: confirm error responses display correctly in the UI (e.g., rate limit, validation error)
- Verify logs: confirm structured JSON logs appear in the Render dashboard
- Verify HTTPS: both frontend and backend are served over HTTPS (automatic on both platforms)

### API Key Safety

- Use a separate Anthropic API key for production (never share with development)
- If using the OpenAI fallback, use a separate production key for that as well
- Set a monthly spend limit in the Anthropic console

### SQLite → PostgreSQL Migration (if needed)

- If persistent storage is required, update `schema.prisma` provider from `sqlite` to `postgresql`
- Update `DATABASE_URL` to a PostgreSQL connection string (e.g., Supabase free tier or Render Postgres)
- Run `npx prisma migrate dev` to regenerate migrations
- Test locally with a Docker PostgreSQL container before deploying

## Acceptance Criteria

- Frontend is live on a `.vercel.app` URL and loads correctly
- Backend is live on a `.onrender.com` URL and responds to `GET /api/health` with `{ status: "ok" }`
- A user can type a natural language prompt on the frontend and receive generated SQL back
- CORS is correctly configured — no cross-origin errors in the browser console
- Structured JSON logs appear in the Render dashboard
- Production API keys are set in hosting dashboards, not in code or config files
- The development `.env` and production environment variables use different API keys

## References

- Requirements Section 5.4 (Deployment — Vercel + Render)
- Requirements Section 5.5 (API key safety)
- Requirements Section 13 (Environment variables)
- Requirements Section 17 (Deployment configuration)
- Requirements Section 17.4 (SQLite → PostgreSQL migration path)
- Requirements Section 23.4 (Secrets rotation schedule)
- Requirements Section 14, Step 22
