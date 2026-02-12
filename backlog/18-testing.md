# 18 — Testing (Vitest, Supertest, React Testing Library)

## Goal

Set up the test infrastructure and write the Priority 1 and Priority 2 tests. These cover the critical path (prompt in → SQL out) and the error/edge cases that would degrade the experience.

## What to Build

### Test Setup

**Server (`server/`):**
- Install: `vitest`, `supertest`
- Add to `package.json`:
  ```json
  { "scripts": { "test": "vitest run", "test:watch": "vitest", "test:coverage": "vitest run --coverage" } }
  ```
- Create `server/vitest.config.js` if needed

**Client (`client/`):**
- Install: `vitest`, `@testing-library/react`, `@testing-library/jest-dom`, `jsdom`
- Add same test scripts to `client/package.json`
- Configure Vitest to use jsdom environment

### LLM Mock (`server/src/services/__mocks__/llm.service.js`)

- Mock `generateSQL` returning a fixed `{ sql, explanation }` response
- Mock `explainSQL` returning a fixed `{ explanation }` response
- Use `vi.fn()` so tests can assert call counts and arguments

### Priority 1 Tests (Must Pass)

| Test File | What to Test |
|---|---|
| `llm.service.test.js` | `parseLLMResponse`: clean JSON, markdown-fenced JSON, preamble text, garbage input → LLM_PARSE_ERROR, missing fields → LLM_PARSE_ERROR |
| `query.validator.test.js` | Rejects empty prompt, rejects prompt > 2,000 chars, rejects missing required fields, accepts valid input, rejects null bytes |
| `contentModerator.test.js` | Blocks "get all passwords", blocks "select api_key", flags "UNION SELECT" as suspicious, flags "' OR 1=1" as suspicious, allows normal prompts |
| `deduplicator.test.js` | Two identical concurrent requests → one LLM call, different requests → separate LLM calls |
| `query.routes.test.js` | `POST /api/v1/generate` → 200 with valid input (mocked LLM), → 400 for empty prompt, → 400 for blocked content, → 502 when LLM is down (mock failure) |

### Priority 2 Tests (Should Pass)

| Test File | What to Test |
|---|---|
| `errorHandler.test.js` | Each AppError code → correct HTTP status and error envelope, unknown errors → 500 with INTERNAL_ERROR, no stack traces in response body |
| `rateLimiter.test.js` | Requests under limit → allowed, requests over limit → 429 with RATE_LIMIT_EXCEEDED |
| `api.test.js` (client) | Returns parsed data on success, returns error object on network failure, returns error object on 4xx/5xx |

## Acceptance Criteria

- `npm test` passes in both `client/` and `server/`
- All Priority 1 tests are written and passing
- All Priority 2 tests are written and passing
- The LLM is never called in any test (mocked)
- Tests run in under 10 seconds

## References

- Requirements Section 15 (Full testing strategy)
- Requirements Section 15.1 (Testing stack)
- Requirements Section 15.2 (What to test — prioritized)
- Requirements Section 15.3 (Mocking the LLM)
- Requirements Section 15.4 (Running tests)
- Requirements Section 14, Step 12
