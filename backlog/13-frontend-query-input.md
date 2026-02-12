# 13 — Frontend QueryInput Component

## Goal

Build the main input component where users type their natural language prompt and submit it. Wire it to the backend API.

## What to Build

### API Service (`client/src/services/api.js`)

Complete the `generateSQL` function:
- `POST` to `${API_BASE}/api/v1/generate` with `{ prompt, schema, dialect }`
- On success: return `{ success: true, data: { sql, explanation } }`
- On failure: log to `console.error`, return `{ success: false, error: { message: '...' } }` with a user-friendly message
- Never expose raw error objects or stack traces to the UI
- Handle network errors, 4xx, and 5xx responses

### QueryInput Component (`client/src/components/QueryInput.jsx`)

Handle four states per Section 16.1:

1. **Empty/Default**: Textarea with placeholder "Describe the data you need..." (from strings.js). Generate SQL button below.
2. **Loading**: Button text changes to "Generating..." with spinner, button disabled, textarea disabled. Prompt text stays visible.
3. **Success**: Pass the result up to the parent (via callback prop) so SqlOutput can display it. Input may clear or stay — user preference.
4. **Error**: Red error banner below the input area with the error message. Prompt text preserved. Button re-enabled.

Implementation details:
- Use a `<textarea>` element (not a div) for accessibility
- The textarea must have a `<label>` or `aria-label`
- Submit on button click (not on Enter, since Enter should create newlines in the textarea)
- Pass `schema` and `dialect` as props (they'll come from SchemaInput later)

### LoadingSpinner (`client/src/components/LoadingSpinner.jsx`)

- Simple spinner component used inside the button during loading state

### Integrate into HomePage

- Add QueryInput to the left column of the HomePage layout
- Wire the onResult callback to pass data to the right column (SqlOutput placeholder for now)

## Acceptance Criteria

- User can type a prompt and click "Generate SQL"
- While loading: button shows "Generating..." and is disabled, textarea is disabled
- On success: result data is passed to parent component
- On failure: error banner appears below input, prompt text preserved, button re-enabled
- All text comes from strings.js
- Textarea has proper label/aria-label for accessibility

## References

- Requirements Section 16.1 (QueryInput state matrix)
- Requirements Section 16.3 (Wireframes — input area positioning)
- Requirements Section 16.4 (Accessibility — labels, keyboard navigation)
- Requirements Section 17.1 (API_BASE resolution)
- Requirements Section 19.3 (Frontend API error handling)
- Requirements Section 14, Step 14
