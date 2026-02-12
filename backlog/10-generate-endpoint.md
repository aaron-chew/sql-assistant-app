# 10 — Generate SQL Endpoint (Full Wiring)

## Goal

Wire up the complete `POST /api/v1/generate` endpoint from route to controller to service. This is the primary feature of the app — user sends a natural language prompt, gets SQL back.

## What to Build

### Controller (`server/src/controllers/query.controller.js`)

Implement the `generate` handler:
1. Run content moderation on `req.body.prompt` — if blocked, throw `AppError('VALIDATION_ERROR', ...)`; if suspicious, log at `warn` and continue
2. Call `llm.service.generateSQL(prompt, schema, dialect)`
3. If deduplication is active (`req._dedup`), resolve the dedup promise with the result
4. Return the success response envelope:
   ```json
   { "success": true, "data": { "sql": "...", "explanation": "..." } }
   ```
5. Log: validation outcome, prompt length, whether schema was provided (at `debug` level)

### Route Wiring (`server/src/routes/v1/query.routes.js`)

- Replace the placeholder handler with the real chain:
  - `validateGenerateInput` (from query.validator.js) → `deduplicator` → `queryController.generate`

### Update `server/src/index.js`

- Ensure `express.json()` middleware is registered (for parsing request bodies)
- Ensure the v1 routes are mounted at `/api/v1`

## Acceptance Criteria

- `POST /api/v1/generate` with `{ "prompt": "Get all users" }` returns a 200 response with `{ success: true, data: { sql, explanation } }`
- The response matches the example format in Section 7 of the requirements
- Invalid input (empty prompt, too-long prompt) returns 400 with the error envelope
- Blocked content returns 400 with `VALIDATION_ERROR`
- If the LLM is down, returns 502 with `LLM_UNAVAILABLE`
- The full request is logged: method, path, status, duration, and request ID
- The LLM call is logged: model, tokens, response time

## References

- Requirements Section 7 (API endpoints — generate example)
- Requirements Section 8 (Data flow)
- Requirements Section 12.1 (Content moderation integration)
- Requirements Section 19.2 (What to log at each layer)
- Requirements Section 14, Step 13
