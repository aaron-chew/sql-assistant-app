# 06 — LLM Prompt Templates

## Goal

Write and version the system prompts that instruct the LLM to generate SQL and explain SQL. These are the most important pieces of "code" in the app — they determine the quality of every response.

## What to Build

### Prompt Templates (`server/src/prompts/index.js`)

- **Generate SQL prompt** (`GENERATE_SQL_PROMPT`):
  - Role definition: "You are a SQL expert assistant"
  - Output format: return ONLY a valid JSON object with `sql` and `explanation` fields
  - No markdown fences, no commentary outside the JSON
  - Schema context injection using `<user_schema>` tags
  - Dialect instruction
  - Safety constraints: default to SELECT; only generate destructive operations (DROP, DELETE, TRUNCATE, ALTER, UPDATE, INSERT) if explicitly requested
  - User input isolation using `<user_prompt>` tags to defend against prompt injection
  - Handle ambiguity: make reasonable assumptions and explain them

- **Explain SQL prompt** (`EXPLAIN_SQL_PROMPT`):
  - Role definition: "You are a SQL expert assistant"
  - Output format: return ONLY a valid JSON object with `explanation` (plain-English breakdown) and optionally `suggestions` (array of improvement tips)
  - User SQL input isolated in `<user_sql>` tags
  - No markdown fences, no commentary outside the JSON

- Include a version comment at the top of each prompt (e.g., `// Prompt v1.0 — 2026-02-13 — Initial version`)

- Export a function for each prompt that takes the dynamic values (userPrompt, schema, dialect / userSql) and returns the assembled prompt string

## Acceptance Criteria

- `getGenerateSQLPrompt({ prompt, schema, dialect })` returns a fully assembled system prompt string with user input wrapped in XML tags
- `getExplainSQLPrompt({ sql })` returns a fully assembled system prompt string with SQL wrapped in XML tags
- Prompts are stored in a single file, not inline in service code
- Each prompt has a version comment

## References

- Requirements Section 9.1 (System prompt design — full specification and example)
- Requirements Section 9.5 (Prompt versioning)
- Requirements Section 5.5 (Prompt injection defense — XML tag isolation)
- Requirements Section 14, Step 8
