# 05 — Versioned API Routing & Input Validation

## Goal

Set up the `/api/v1/` route structure and build input validation with Zod so all incoming data is checked before it reaches any business logic.

## What to Build

### Versioned Route Structure

- `server/src/routes/v1/index.js` — combines all v1 routes, exports a router
- `server/src/routes/v1/query.routes.js` — defines `POST /generate` and `POST /explain` (stub handlers for now that return a placeholder response)
- Register v1 routes in `index.js` at `/api/v1`

### Input Validation (`server/src/validators/query.validator.js`)

- Install `zod`
- Create validation schemas:
  - **Generate schema**: `prompt` (required string, max 2,000 chars), `schema` (optional string, max 5,000 chars), `dialect` (optional string, one of: 'postgresql', 'mysql', 'sqlite', 'sqlserver')
  - **Explain schema**: `sql` (required string, max 5,000 chars)
- Strip or reject input containing null bytes or control characters
- Export validation middleware functions that validate `req.body` and either call `next()` or throw an `AppError('VALIDATION_ERROR', ...)` with a descriptive message

### Content Moderation (`server/src/validators/contentModerator.js`)

- Implement the `moderateInput(prompt)` function from Section 12.1
- **Blocked patterns** (reject with `VALIDATION_ERROR`): requests for credentials, passwords, API keys, secrets
- **Suspicious patterns** (log at `warn`, allow): SQL injection test strings, system metadata references
- Export the function for use in the controller layer

## Acceptance Criteria

- `POST /api/v1/generate` with an empty body returns 400 with `VALIDATION_ERROR`
- `POST /api/v1/generate` with a prompt over 2,000 characters returns 400 with `VALIDATION_ERROR`
- `POST /api/v1/generate` with a valid prompt returns a 200 placeholder response
- A prompt containing "get all passwords" is rejected with `VALIDATION_ERROR`
- A prompt containing "UNION SELECT" is allowed but logged at `warn` level
- `POST /api/v1/explain` with valid SQL returns a 200 placeholder response

## References

- Requirements Section 5.5 (Input sanitization — max lengths, control characters)
- Requirements Section 7.0 (API versioning)
- Requirements Section 7 (Endpoint definitions)
- Requirements Section 12 (Content moderation)
- Requirements Section 14, Step 7
