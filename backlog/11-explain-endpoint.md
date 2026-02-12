# 11 — Explain SQL Endpoint

## Goal

Wire up `POST /api/v1/explain` — user pastes SQL, gets a plain-English explanation back.

## What to Build

### Controller (`server/src/controllers/query.controller.js`)

Add the `explain` handler:
1. Call `llm.service.explainSQL(sql)` with the validated SQL from the request body
2. Return the success response envelope:
   ```json
   { "success": true, "data": { "explanation": "...", "suggestions": [...] } }
   ```
3. `suggestions` is optional — include it if the LLM returns it, omit if not

### Route Wiring (`server/src/routes/v1/query.routes.js`)

- Wire `POST /explain`:
  - `validateExplainInput` (from query.validator.js) → `deduplicator` → `queryController.explain`

## Acceptance Criteria

- `POST /api/v1/explain` with `{ "sql": "SELECT * FROM users" }` returns a 200 response with `{ success: true, data: { explanation } }`
- Missing or empty `sql` field returns 400 with `VALIDATION_ERROR`
- SQL over 5,000 characters returns 400 with `VALIDATION_ERROR`
- Explain responses are NOT cached (every request gets a fresh LLM call)
- If the LLM is down, returns 502 with `LLM_UNAVAILABLE`

## References

- Requirements Section 7 (API endpoints — explain)
- Requirements Section 9.1 (Explain SQL prompt)
- Requirements Section 10.2 (Cache rules — do not cache explain)
- Requirements Section 14, Step 16
