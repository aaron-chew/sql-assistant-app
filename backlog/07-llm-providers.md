# 07 — LLM Provider Abstraction (Anthropic + OpenAI Fallback)

## Goal

Build a provider abstraction layer so the app can call Claude as the primary LLM and fall back to OpenAI if Claude is down. Each provider exports the same interface so they're interchangeable.

## What to Build

### Provider Interface

Each provider module exports:
```js
{
  name: 'provider-name',
  async generate(systemPrompt, userPrompt, options) → { rawText }
}
```

### Anthropic Provider (`server/src/services/providers/anthropic.provider.js`)

- Install the `@anthropic-ai/sdk` package
- Implement the `generate` function:
  - Use the Anthropic SDK to call the Messages API
  - Model: `claude-haiku-4-5-20251001` (cheapest, upgrade later if needed)
  - `max_tokens`: 1024
  - `temperature`: 0 (deterministic output for SQL)
  - Set a 15-second timeout on the API call
  - Return the raw text response from the model
  - On error, throw with the status code attached so the caller can decide whether to retry/fallback

### OpenAI Provider (`server/src/services/providers/openai.provider.js`)

- Install the `openai` package
- Implement the same `generate` interface
- Model: `gpt-4o-mini` (cheapest capable model)
- Same `max_tokens`, `temperature`, and timeout settings
- This provider is optional — if `OPENAI_API_KEY` is not set, it should gracefully indicate it's unavailable

### Per-Provider Retry Logic

Each provider should implement retry-once behavior internally before the cross-provider fallback fires:

- On 429 (rate limited) or 5xx errors from the LLM API, **retry once after a 2-second delay**
- If the retry also fails, throw the error (which triggers the cross-provider fallback)
- On 4xx errors (other than 429), throw immediately — these indicate a bug in the prompt, not a transient failure
- Log every retry attempt at `warn` level with the provider name, error code, and attempt number

### Fallback Logic (`server/src/services/llm.service.js` — partial, provider selection only)

- Import both providers
- Implement `callWithFallback(systemPrompt, userPrompt, options)`:
  - Try the primary provider first (which internally retries once on transient errors)
  - If the primary provider fails after its retry → log at `warn` and try the fallback provider
  - On 4xx errors (other than 429) → throw immediately (bad request, don't retry with another provider)
  - If fallback also fails → throw `AppError('LLM_UNAVAILABLE', ...)`
- Determine primary/fallback from `config.llmPrimaryProvider`

## Acceptance Criteria

- With a valid `ANTHROPIC_API_KEY`, calling `anthropicProvider.generate(...)` returns raw text from Claude
- With no `OPENAI_API_KEY` set, the fallback provider reports itself as unavailable without crashing
- `callWithFallback` tries the primary provider first; if it fails with a 5xx, it tries the fallback
- 4xx errors from the primary provider are thrown immediately without fallback
- Every fallback event is logged at `warn` level

## References

- Requirements Section 9.3 (Model parameters)
- Requirements Section 9.4 (Timeout & retry logic)
- Requirements Section 11 (Multi-provider LLM fallback)
- Requirements Section 14, Step 9
