# 09 — Request Deduplication & Circuit Breaker

## Goal

Add two protective mechanisms: a deduplication middleware that prevents duplicate concurrent LLM calls (e.g., double-clicks), and a circuit breaker that stops hammering a downed LLM provider.

## What to Build

### Request Deduplication (`server/src/middleware/deduplicator.js`)

- Before sending a request to the LLM, generate a SHA-256 hash of the request payload (`prompt + schema + dialect`)
- Maintain an in-flight request `Map` (hash → Promise)
- If the hash is already in the map, wait for the existing call to finish and return the same result
- If not, add it to the map, let the controller proceed, and clean up on response finish
- Include a 30-second safety timeout to clean up stale entries
- Apply this middleware only to `/api/v1/generate` and `/api/v1/explain` — not the health check

### Circuit Breaker (`server/src/utils/circuitBreaker.js`)

- Implement the `CircuitBreaker` class with three states: CLOSED (healthy), OPEN (failing), HALF_OPEN (testing)
- Configuration: `failureThreshold` (default 5), `resetTimeMs` (default 30,000ms)
- When CLOSED: pass requests through. Track failures.
- When failures hit threshold: transition to OPEN. Reject all requests immediately for `resetTimeMs`.
- After `resetTimeMs`: transition to HALF_OPEN. Allow one request through.
  - If it succeeds: transition back to CLOSED, reset failure count
  - If it fails: transition back to OPEN, reset timer

### Integration

- Wrap each LLM provider call in a circuit breaker instance in `llm.service.js`
- When a circuit breaker is OPEN, skip directly to the fallback provider
- If both circuit breakers are OPEN, return `AppError('LLM_UNAVAILABLE', ...)`

## Acceptance Criteria

- Two identical concurrent `POST /api/v1/generate` requests result in only one LLM call; both get the same response
- Different requests are NOT deduplicated
- After 5 consecutive LLM failures, the circuit breaker opens and subsequent requests fail immediately without calling the LLM
- After the reset time, one test request is allowed through (HALF_OPEN)
- A successful test request closes the circuit breaker

## References

- Requirements Section 10.1 (Request deduplication — full implementation)
- Requirements Section 11.2 (Circuit breaker — full implementation)
- Requirements Section 14, Step 11
