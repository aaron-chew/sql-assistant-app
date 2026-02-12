# 16 — Frontend HistoryPanel & Session Persistence

## Goal

Build the query history panel that shows past queries from the current session, stored in `sessionStorage`.

## What to Build

### HistoryPanel Component (`client/src/components/HistoryPanel.jsx`)

Handle states per Section 16.1:

1. **Empty**: "No queries yet. Try generating one!" (from strings.js)
2. **With entries**: List of past queries, most recent first. Each entry shows:
   - The prompt text (truncated if long)
   - Relative timestamp (e.g., "2 min ago")
   - Clickable — clicking an entry reloads that prompt and result into the main UI

Desktop: Always visible in the right column below the SQL output
Mobile: Collapsible accordion (closed by default), label shows count (e.g., "History (3 queries)")

### Session Storage Logic

- Store queries in `sessionStorage` under the key `queryHistory`
- On each successful generate:
  - Read existing history from sessionStorage
  - Prepend the new entry: `{ prompt, sql, explanation, timestamp: Date.now() }`
  - Cap at 50 entries
  - Write back to sessionStorage
- On page load, read from sessionStorage to restore history
- `sessionStorage` clears automatically when the tab is closed — no data retention concerns

### Time Ago Utility

- Simple function to convert a timestamp to relative time (e.g., "just now", "2 min ago", "1 hour ago")
- Use the `timeAgo` function from strings.js or create a small utility

### Integrate into HomePage

- Add HistoryPanel to the right column below SqlOutput
- When a history entry is clicked, populate the QueryInput with the prompt and SqlOutput with the result

## Acceptance Criteria

- Empty state shows the placeholder message
- After generating a query, it appears at the top of the history list
- History persists across page refreshes (same tab)
- History is cleared when the tab is closed
- Clicking a history entry reloads the prompt and result
- On mobile, history is in a collapsible accordion showing the entry count
- Maximum 50 entries stored

## References

- Requirements Section 4.1 (Query history — core feature)
- Requirements Section 16.1 (HistoryPanel state matrix)
- Requirements Section 16.3 (Wireframes — history positioning)
- Requirements Section 16.5 (Session history persistence — implementation)
- Requirements Section 14, Step 17
