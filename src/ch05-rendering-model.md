# React Rendering Model

## Component Execution

A component re-executes when:

- props change
- state changes

### How Props Change

Props change when the **parent component re-renders** and passes new values down. A component cannot change its own props.

When `Parent`'s state changes → `Parent` re-executes → `Child` receives new prop → `Child` re-executes.

## Virtual DOM & Reconciliation

React:

1. Executes component → creates virtual DOM
2. Compares with previous virtual DOM (called "diffing" or "reconciliation")
3. Updates _only changed parts_ of real DOM.

## Strict Mode

In `<StrictMode>` (usually in `main.jsx` or `index.js`), components are rendered **twice** during development.
- This helps detect side effects and impure functions.
- This behavior **does not happen** in production or `npm run build`.
