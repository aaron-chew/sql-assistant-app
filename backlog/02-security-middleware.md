# 02 — Security Middleware (Helmet, CORS, Rate Limiter)

## Goal

Add the three core security middleware layers to the Express server: HTTP security headers (Helmet), cross-origin access control (CORS), and rate limiting. These protect against common web attacks and prevent API abuse.

## What to Build

### Helmet (`server/src/index.js`)

- Install `helmet`
- Add as the first middleware in the Express app
- Configure Content Security Policy per Section 23.1
- Configure HSTS per Section 23.2 (max-age 1 year, includeSubDomains, preload)

### CORS (`server/src/index.js`)

- Install `cors`
- Configure with `origin` set to `config.allowedOrigin` (from the config module built in feature 01)
- Only allow `GET` and `POST` methods
- Never use `origin: '*'` — the allowed origin comes from the environment variable

### Rate Limiter (`server/src/middleware/rateLimiter.js`)

- Install `express-rate-limit`
- Create a rate limiter middleware:
  - Window: 1 minute
  - Max: 20 requests per IP per window
  - Use `standardHeaders: true`, `legacyHeaders: false`
  - On limit exceeded, return the standard error envelope: `{ success: false, error: { code: 'RATE_LIMIT_EXCEEDED', message: 'Too many requests. Please wait a moment.' } }`
- Apply the rate limiter globally in `index.js`

## Acceptance Criteria

- Response headers include `Content-Security-Policy`, `X-Frame-Options`, `Strict-Transport-Security`, and other Helmet defaults
- Requests from `http://localhost:5173` are allowed; requests from other origins are blocked with a CORS error
- Sending 21+ requests in under a minute from the same IP returns a 429 status with the `RATE_LIMIT_EXCEEDED` error envelope
- All middleware is applied in the correct order: Helmet first, then CORS, then rate limiter

## References

- Requirements Section 5.5 (CORS, Helmet, Rate Limiting)
- Requirements Section 23.1 (Content Security Policy)
- Requirements Section 23.2 (HSTS)
- Requirements Section 14, Step 3
