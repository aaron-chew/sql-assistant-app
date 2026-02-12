# 08 — LLM Service (Response Parsing & Caching)

## Goal

Build the core LLM service that constructs prompts, calls the LLM provider, parses the response, validates the output, and caches results. This is the "brain" of the backend.

## What to Build

### Response Parsing (`server/src/services/llm.service.js`)

Implement `parseLLMResponse(rawText)`:
- Strip markdown code fences (`\`\`\`json ... \`\`\``)
- Extract JSON substring (find first `{` to last `}`)
- Parse JSON; if parsing fails, throw `AppError('LLM_PARSE_ERROR', ...)`
- Validate required fields exist (`sql` + `explanation` for generate; `explanation` for explain)
- If generated SQL starts with a destructive keyword (DROP, DELETE, TRUNCATE, ALTER) and the user didn't explicitly request it, strip the query and return a warning
- Log malformed responses at `warn` level, full raw response at `debug` level

### Response Caching

- Install `node-cache`
- Create cache instance in `server/src/utils/cache.js` with TTL of 1 hour, check period of 10 minutes
- In the LLM service:
  - Before calling the LLM, generate a SHA-256 hash of `prompt + schema + dialect` and check the cache
  - On cache hit, log at `debug` level and return cached result
  - On cache miss, call the LLM, store the result in cache, return it
  - Only cache `generate` responses — do NOT cache `explain` responses
  - Never cache error responses
  - Cap at ~500 entries

### Public API

Export two functions from `llm.service.js`:
- `generateSQL(prompt, schema, dialect)` — assembles the generate prompt, calls LLM with fallback, parses response, caches result
- `explainSQL(sql)` — assembles the explain prompt, calls LLM with fallback, parses response (no caching)

### Output Validation (Enhanced)

- Check generated SQL for credential-related column references (`password`, `api_key`, `secret`, `token`) — add a warning notice to the response
- Check for excessive complexity (>10 JOINs) — add a warning notice

## Acceptance Criteria

- `generateSQL('get all users', null, 'postgresql')` returns `{ sql, explanation }` from the LLM
- Calling `generateSQL` with the same arguments twice returns a cached result on the second call (verified via debug log)
- `explainSQL('SELECT * FROM users')` returns `{ explanation }` and is never cached
- A raw LLM response wrapped in markdown fences is correctly parsed
- A raw LLM response with preamble text before JSON is correctly parsed
- Garbage LLM output throws `LLM_PARSE_ERROR`
- Responses mentioning password columns include a warning notice

## References

- Requirements Section 9.2 (LLM response parsing — full specification with failure modes)
- Requirements Section 10.2 (Response caching)
- Requirements Section 12.2 (Output validation — credential columns, complexity)
- Requirements Section 14, Step 10
