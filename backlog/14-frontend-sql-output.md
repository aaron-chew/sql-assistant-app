# 14 — Frontend SqlOutput Component & Copy to Clipboard

## Goal

Build the output component that displays the generated SQL with syntax highlighting, a copy-to-clipboard button, and the plain-English explanation.

## What to Build

### Copy Utility (`client/src/utils/clipboard.js`)

- Implement a `copyToClipboard(text)` function using the Clipboard API (`navigator.clipboard.writeText`)
- Return a boolean indicating success/failure
- Fallback for older browsers: create a temporary textarea, select, and `document.execCommand('copy')`

### SqlOutput Component (`client/src/components/SqlOutput.jsx`)

Handle four states per Section 16.1:

1. **Empty/Default**: Show "Your SQL will appear here." (from strings.js)
2. **Loading**: Skeleton/pulse animation (Tailwind `animate-pulse` on gray blocks), text "Generating your query..."
3. **Success**:
   - SQL displayed in a code block with monospace font
   - Consider using `react-simple-code-editor` or `CodeMirror` for SQL syntax highlighting (or start with a simple `<pre><code>` block and upgrade later)
   - Copy to clipboard button below the SQL block
   - On copy: button text changes to "Copied!" for 2 seconds, then reverts (accessibility feedback per Section 16.4)
   - Explanation section below the SQL with the plain-English breakdown
4. **Error**: Hidden (errors are shown in the QueryInput area)

Accessibility:
- The SQL output area must have `role="region"` and `aria-live="polite"` so screen readers announce new results
- The copy button must provide text feedback, not just a visual flash

### Integrate into HomePage

- Add SqlOutput to the right column of the HomePage layout
- Wire it to receive data from QueryInput's result callback

## Acceptance Criteria

- When no query has been submitted, shows the empty state message
- During loading, shows a skeleton animation
- On success, displays the SQL in a code block with a working copy button
- Copy button text changes to "Copied!" for 2 seconds after clicking
- Explanation text appears below the SQL
- Screen readers announce new results via `aria-live="polite"`
- When a new query errors, the output area reverts to its empty state (no stale results)

## References

- Requirements Section 4.1 (Copy to clipboard — core feature)
- Requirements Section 5.1 (Code display — React Simple Code Editor or CodeMirror)
- Requirements Section 16.1 (SqlOutput state matrix)
- Requirements Section 16.3 (Wireframes — output area, loading state, error state)
- Requirements Section 16.4 (Accessibility — aria-live, copy feedback)
- Requirements Section 14, Step 14
