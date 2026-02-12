# 04 — Request Logger Middleware & Health Check Endpoint

## Goal

Add request logging so every HTTP request is traced with method, path, status code, and duration. Add the health check endpoint that monitoring tools and hosting platforms will use.

## What to Build

### Request Logger (`server/src/middleware/requestLogger.js`)

- Express middleware that logs every incoming request
- Generate a unique request ID using `crypto.randomUUID()` — attach it to `req.requestId`
- On response finish, log: method, path, status code, response time in ms, and request ID
- Log at `info` level
- Example output: `INFO: POST /api/v1/generate — 200 — 1847ms — requestId: abc-123`

### Health Check Route (`server/src/routes/health.routes.js`)

- `GET /api/health` — returns `{ status: "ok" }` with a 200 status code
- This endpoint is unversioned (not under `/api/v1/`) because monitoring tools shouldn't break on API version bumps

### Update `server/src/index.js`

- Register `requestLogger` middleware before routes (but after security middleware)
- Register the health check route at `/api/health`

## Acceptance Criteria

- Every request (including health checks) produces a log line with method, path, status, duration, and request ID
- `GET /api/health` returns `{ status: "ok" }` with 200
- The request ID is unique per request

## References

- Requirements Section 7.0 (Health check is unversioned)
- Requirements Section 17.3 (Health check for hosting platforms)
- Requirements Section 19.2 (requestLogger.js)
- Requirements Section 19.4 (Request IDs)
- Requirements Section 14, Steps 4–5
