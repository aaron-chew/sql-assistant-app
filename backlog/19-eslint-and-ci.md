# 19 — ESLint & GitHub Actions CI

## Goal

Set up linting for code quality and a CI pipeline that runs tests, linting, and dependency audits on every push and pull request.

## What to Build

### ESLint Setup

**Server (`server/`):**
- Install `eslint`
- Initialize config with `npx eslint --init` (or create `.eslintrc.js` manually)
- Rules: catch unused variables, common bugs, enforce consistency
- Fix any existing linting issues

**Client (`client/`):**
- Install `eslint` and React-specific plugins (`eslint-plugin-react`, `eslint-plugin-react-hooks`)
- Configure for React/JSX
- Fix any existing linting issues

### GitHub Actions CI (`.github/workflows/ci.yml`)

Create a workflow that runs on every `push` and `pull_request`:

1. **Server — Install**: `cd server && npm ci`
2. **Server — Lint**: `cd server && npx eslint src/`
3. **Server — Audit**: `cd server && npm audit --audit-level=high`
4. **Server — Test**: `cd server && npm test`
5. **Client — Install**: `cd client && npm ci`
6. **Client — Lint**: `cd client && npx eslint src/`
7. **Client — Audit**: `cd client && npm audit --audit-level=high`
8. **Client — Test**: `cd client && npm test`

Use Node.js 20 and `actions/checkout@v4` + `actions/setup-node@v4`.

## Acceptance Criteria

- `npx eslint src/` passes with zero errors in both client and server
- The GitHub Actions workflow runs on push and PR
- The pipeline includes: install, lint, audit, and test for both client and server
- A failing test or lint error causes the CI job to fail

## References

- Requirements Section 5.4 (ESLint)
- Requirements Section 15.5 (CI integration — full YAML)
- Requirements Section 23.3 (Dependency vulnerability scanning)
- Requirements Section 14, Steps 20–21
