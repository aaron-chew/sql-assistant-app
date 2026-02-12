# 03 — Error Handling (AppError Class & Error Handler Middleware)

## Goal

Create a typed error system so every error in the app follows a consistent pattern. Controllers and services throw typed `AppError` instances; a single global middleware catches them and returns the standard error envelope to the client.

## What to Build

### AppError Class (`server/src/errors/AppError.js`)

- Custom error class extending `Error`
- Constructor takes: `code` (string), `message` (string), `statusCode` (number, optional)
- Supports all error codes from Section 7.1:
  - `VALIDATION_ERROR` → 400
  - `RATE_LIMIT_EXCEEDED` → 429
  - `LLM_UNAVAILABLE` → 502
  - `LLM_RATE_LIMITED` → 429
  - `LLM_PARSE_ERROR` → 502
  - `DUPLICATE_REQUEST` → 409
  - `INTERNAL_ERROR` → 500
- Include a static map of error codes to HTTP status codes so the error handler can look them up

### Error Handler Middleware (`server/src/middleware/errorHandler.js`)

- Express error-handling middleware (4 arguments: `err, req, res, next`)
- If `err` is an instance of `AppError`, use its `code` and `message` to build the response
- If `err` is an unknown/unexpected error, return `INTERNAL_ERROR` with a generic message — never leak stack traces, file paths, or internal details to the client
- Always return the standard error envelope: `{ success: false, error: { code, message } }`
- Log the full error (including stack trace) server-side via the Winston logger at `error` level
- Register as the last middleware in `index.js` (after all routes)

### Update `server/src/index.js`

- Import and register the error handler middleware after all routes
- Add a catch-all 404 handler for undefined routes that returns `{ success: false, error: { code: 'NOT_FOUND', message: 'Endpoint not found.' } }`

## Acceptance Criteria

- Throwing `new AppError('VALIDATION_ERROR', 'Prompt is required.')` in any route handler results in a 400 response with the correct error envelope
- Unknown errors return 500 with `INTERNAL_ERROR` code and a generic message — no stack traces in the response body
- The full error with stack trace appears in the server logs
- Hitting a non-existent route returns a 404 with the error envelope

## References

- Requirements Section 7.1 (Error response contract)
- Requirements Section 19.2 (errorHandler.js logging)
- Requirements Section 14, Step 6
