# 17 — Frontend Polish (Loading States, Accessibility, Responsive)

## Goal

Final pass on the frontend to ensure all loading states, error states, accessibility requirements, and responsive behavior match the wireframes and spec exactly.

## What to Check & Fix

### Loading States

- QueryInput: button shows "Generating..." with spinner, button + textarea disabled
- SqlOutput: skeleton/pulse animation with "Generating your query..." text
- No UI element is left in an ambiguous state during loading

### Error States

- Error banner appears below the input area (not in the output column)
- Prompt text is preserved on error so users can edit and retry
- Generate button re-enables immediately after error
- Output area reverts to empty/default state on error — no stale results shown alongside an error
- User-friendly error messages from strings.js for each error code (generic, rateLimit, llmUnavailable)

### Accessibility (Section 16.4)

- All interactive elements are keyboard-navigable (use native `<button>`, `<textarea>`, `<a>` — not div-based controls)
- All form inputs have `<label>` or `aria-label`
- SQL output area has `role="region"` and `aria-live="polite"`
- Color contrast meets WCAG AA (4.5:1 for body text)
- Copy button provides text feedback ("Copied!" for 2 seconds)

### Responsive Layout

- Desktop (>= 1024px): two-column layout matches wireframe
- Tablet (768–1023px): single column, full width
- Mobile (< 768px): single column, reduced padding, collapsible history
- Privacy notice visible at all breakpoints

### Final Touches

- No hardcoded strings in JSX — everything from strings.js
- All API error messages map to user-friendly text
- Generate button does not allow submission of empty/whitespace-only prompts

## Acceptance Criteria

- Manual testing at desktop, tablet, and mobile breakpoints shows correct layout
- Keyboard-only navigation works for the full flow: type prompt → submit → copy result → view history
- Screen reader testing (or manual `aria-live` verification) confirms new results are announced
- All four states (empty, loading, success, error) are visually distinct and match the wireframes

## References

- Requirements Section 16 (Full UX specification)
- Requirements Section 14, Step 19
