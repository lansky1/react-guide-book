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

## How Batching Works

React batches state updates for performance. Understanding this helps avoid common bugs.

1. React has an update queue.
2. Everytime we call `setState`, React doesn't update state immediately.
3. Instead it creates an update object and pushes it into an internal queue.
4. React waits until your event handler finishes, then processes this queue.
5. But each update object stores the computed value.

Suppose count = 5

**Case 1: Value-based updates**

```jsx
setCount(count + 1);
setCount(count + 1);
```

```bash
updates = [
  { type: "value", payload: 6 },
  { type: "value", payload: 6 },
]
```

When React processes the queue:

- First update sets count → 6
- Second update sets count → 6
- Second update overwrites the first
- ⇒ final state = 6

This is why calling twice doesn't increment twice.

**Summary:** React first batches and completes value calculation, then does a single update with the final value. Meaning two `setValue` updates will run first, and then with the final value the new component will be rendered.

**Case 2: Function-based updates**

```jsx
setCount((prev) => prev + 1);
setCount((prev) => prev + 1);
```

```bash
updates = [
  { type: "function", payload: (prev) => prev + 1 },
  { type: "function", payload: (prev) => prev + 1 },
]
```

**Simplified Internal Algorithm**

```javascript
function processQueue(updates, initialState) {
  let state = initialState;

  for (let update of updates) {
    if (update.type === "value") {
      state = update.payload; // already computed
    } else if (update.type === "function") {
      state = update.payload(state); // compute using latest
    }
  }

  return state;
}
