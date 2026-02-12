# 01 — Winston Logger & App Configuration

## Goal

Set up structured logging with Winston and centralize all environment variable loading into a config module. From this point forward, no `console.log` should be used in the backend — all logging goes through Winston.

## What to Build

### Logger (`server/src/utils/logger.js`)

- Install `winston`
- Create a single Winston logger instance that is imported everywhere
- **Development** (`NODE_ENV=development`): log level `debug`, colorized readable text output to console
- **Production** (`NODE_ENV=production`): log level `info`, structured JSON output to console
- Every log entry should include a timestamp

### Config (`server/src/config/index.js`)

- Load environment variables via `dotenv` (already installed in skeleton step)
- Export a single config object with all app settings:
  - `port` (default 3001)
  - `nodeEnv` (default 'development')
  - `logLevel` (default 'debug')
  - `allowedOrigin` (default 'http://localhost:5173')
  - `anthropicApiKey`
  - `openaiApiKey` (optional)
  - `llmPrimaryProvider` (default 'anthropic')
- Validate that required keys exist on startup; log a warning (not a crash) if optional keys are missing

### Update `server/src/index.js`

- Import the logger and config module
- Replace any existing `console.log` with `logger.info`
- Use `config.port` instead of hardcoded port
- Log server start: `logger.info('Server started', { port: config.port, env: config.nodeEnv })`

## Acceptance Criteria

- Starting the server prints a structured log line (readable in dev, JSON in production)
- `logger.debug('test')` prints in development but not when `NODE_ENV=production`
- Config module exports all required variables
- No `console.log` remains in any server file

## References

- Requirements Section 5.2 (Winston)
- Requirements Section 13 (Environment variables)
- Requirements Section 19.1 (Winston configuration)
- Requirements Section 19.2 (What to log at each layer)
- Requirements Section 19.5 (What NOT to do)
- Requirements Section 14, Step 2
