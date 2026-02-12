# 15 — Frontend SchemaInput Component

## Goal

Build the optional schema context input that lets users paste their table structure so generated queries match their actual database.

## What to Build

### SchemaInput Component (`client/src/components/SchemaInput.jsx`)

Handle states per Section 16.1:

1. **Empty/Default**: Collapsed accordion with label "Add your table schema (optional)" (from strings.js). Clicking expands to reveal the textarea.
2. **Expanded**: Textarea where users can paste their schema (e.g., `users (id, name, email, created_at), purchases (id, user_id, amount, created_at)`). "Clear" button to empty the textarea.
3. **No loading or error states** — this is local input only, never sent to an API on its own.

Implementation:
- Collapsible accordion using a simple toggle state
- Textarea with `aria-label` for accessibility
- The schema value is lifted up to the parent (HomePage) and passed to QueryInput when generating
- On mobile: accordion starts collapsed to save vertical space

### Integrate into HomePage

- Add SchemaInput above QueryInput in the left column (desktop) / above input section (mobile)
- Pass the schema value down to QueryInput so it's included in the API call

## Acceptance Criteria

- Schema input is collapsed by default, showing only the toggle label
- Clicking the toggle expands the textarea
- User can type/paste schema text
- "Clear" button empties the textarea
- Schema value is passed to the generate API call when the user submits a prompt
- On mobile, the accordion stays collapsed by default

## References

- Requirements Section 4.1 (Schema context input — core feature)
- Requirements Section 16.1 (SchemaInput state matrix)
- Requirements Section 16.3 (Wireframes — schema input positioning)
- Requirements Section 14, Step 18
