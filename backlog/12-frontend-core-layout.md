# 12 — Frontend Core Layout (App Shell, Header, ErrorBoundary, Routing)

## Goal

Build the frontend app shell: the Header, page routing, ErrorBoundary, i18n strings file, and the responsive two-column / single-column layout from the wireframes.

## What to Build

### i18n Strings (`client/src/i18n/strings.js`)

- Create the centralized strings file per Section 21.1
- All user-facing text must come from this file — no hardcoded strings in JSX
- Include all string keys defined in the requirements (app, queryInput, sqlOutput, history, errors, privacy)

### Header (`client/src/components/Header.jsx`)

- App title "SQL Assistant" on the left
- "About" link on the right
- Responsive: works on both desktop and mobile

### ErrorBoundary (`client/src/components/ErrorBoundary.jsx`)

- React Error Boundary wrapping `<App />`
- On crash: show a "Something went wrong" fallback UI instead of a white screen
- Log the error to `console.error`

### App Layout (`client/src/App.jsx`)

- Responsive layout matching the wireframes in Section 16.3:
  - **Desktop (>= 1024px)**: two-column layout — input on left, output on right
  - **Tablet (768px–1023px)**: single column, full width
  - **Mobile (< 768px)**: single column, reduced padding
- Use Tailwind responsive prefixes (`md:`, `lg:`)
- Include the privacy notice at the bottom (text from strings.js)
- Placeholder slots for components that will be built in later features

### Pages (`client/src/pages/`)

- `HomePage.jsx` — the main query interface (contains the two-column layout)
- `AboutPage.jsx` — basic info page about the tool, links to Anthropic/OpenAI data policies

### Routing

- Use React Router (or keep it simple with conditional rendering if only 2 pages)
- `/` → HomePage
- `/about` → AboutPage

### API Service Stub (`client/src/services/api.js`)

- Set up `API_BASE` from `import.meta.env.VITE_API_URL || 'http://localhost:3001'`
- Create stub functions `generateSQL(prompt, schema, dialect)` and `explainSQL(sql)` that will call the backend (implement the actual fetch calls here or leave as stubs for the next feature)

## Acceptance Criteria

- The app renders with the Header showing "SQL Assistant" and an "About" link
- The layout switches between two-column (desktop) and single-column (mobile) based on viewport width
- Wrapping `<App />` in the ErrorBoundary prevents white screens on component crashes
- All visible text comes from `strings.js`
- The privacy notice is visible at the bottom of the page
- Navigation between Home and About works

## References

- Requirements Section 16.1 (Component state matrix)
- Requirements Section 16.2 (Responsive breakpoints)
- Requirements Section 16.3 (Wireframes — desktop, mobile layouts)
- Requirements Section 16.4 (Accessibility requirements)
- Requirements Section 17.1 (Frontend → Backend URL resolution)
- Requirements Section 19.3 (Frontend error handling)
- Requirements Section 21 (i18n preparation)
- Requirements Section 14, Step 15
