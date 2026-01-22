# Appendix

## useState

1. `setCount` with a value as replacing the photo with a new one.
2. `setCount` with a function as giving React instructions on how to modify the photo, no matter when it does the job.
3. The reason functions behave differently is because React does NOT run them immediately.
4. React runs the functions one after another, and each one sees the most recent state.

**State Updates with Previous Value:**
When updating state based on the previous value, **always** pass a function to the state updating function.

```jsx
setIsEditing(!isEditing); // isEditing is false, sets to true
setIsEditing(!isEditing); // isEditing is STILL false here, sets to true
// Result: Both updates use the same stale value, so it only toggles once
```

The rendering happens after the function executes completely, so both calls access the same value if you don't use the functional update form.

## How Batching Works

1. React has an update queue.
2. Everytime we call `setState`, React doesn't update state immediately.
3. Instead it creates an update object and pushes it into an internal queue.
4. React waits until your event handler finishes, then processes this queue.
5. But each update object stores the computed value.

Suppose count = 5

**Case 1**

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

**Case 2**

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
```

## Form Submission and Events

**Common Mistake:** Trying to access form data via `event.target.value` in the submit handler.

**Problem:** `event.target.value` doesn't exist on form submission events. `event.target` refers to the form element itself, not individual input values.

**Solution:** Extract data from component state, then pass it via callback functions.

**Example:**

```jsx
// Parent Component
function App() {
  const [items, setItems] = useState([]);

  function handleSubmit(formData) {
    // Receives the actual data object, not an event
    setItems(prevItems => [...prevItems, formData]);
  }

  return <Form onSubmit={handleSubmit} />;
}

// Child Component
function Form({ onSubmit }) {
  const [formData, setFormData] = useState({ title: "", description: "" });

  function handleSubmit(event) {
    event.preventDefault();
    // Pass the state data, not the event
    onSubmit(formData);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={formData.title}
        onChange={(e) => setFormData({ ...formData, title: e.target.value })}
      />
      {/* other inputs */}
      <button type="submit">Save</button>
    </form>
  );
}
```

**Key Points:**
- Form submissions don't have a `value` property - `event.target` is the `<form>` element
- Form data comes from component state (controlled inputs) or FormData API
- Always call `event.preventDefault()` to stop page reload
- Pass data objects through callbacks, not events
- Callbacks receive your data, not synthetic events
