# HTML for React Developers

This chapter covers HTML elements and patterns commonly used in React applications.

---

## The `<dialog>` Element

The native HTML `<dialog>` element provides built-in support for dialogs and modals.

### Visibility

- **Invisible by default** — requires the `open` attribute or `.showModal()` method to display
- Two display modes:
  | Mode | Usage | Behavior |
  |------|-------|----------|
  | `<dialog open>` | Inline attribute | Displays in normal document flow |
  | `dialog.showModal()` | JavaScript method | Displays as modal with backdrop |

### Basic Example

```jsx
<dialog className="result-modal" open>
  <h2>Dialog Title</h2>
  <form method="dialog">
    <button>Close</button>
  </form>
</dialog>
```

### Form Integration

Using `<form method="dialog">` inside a dialog provides automatic close behavior:

- The dialog closes when the form is submitted (no JavaScript needed)
- Any button inside the form triggers the close on click
- Alternative: use refs with `.showModal()` and `.close()` for programmatic control

### The `onClose` Event

The `onClose` event fires when a `<dialog>` is closed — regardless of *how* it was closed (ESC key, `method="dialog"` form, or `.close()` call).

```jsx
function handleClose() {
  console.log('Dialog closed');
  // Reset form, cleanup, etc.
}

<dialog ref={dialogRef} onClose={handleClose}>
  <form method="dialog">
    <button>Close</button>
  </form>
</dialog>
```

> ⚠️ **Note:** `onClose` does NOT fire if the dialog is hidden by removing the `open` attribute directly.

---

## Form Handling with `onSubmit`

The `onSubmit` prop handles form submission events in React.

```jsx
function handleSubmit(event) {
  event.preventDefault();  // Prevent page reload
  // Access form data
}

<form onSubmit={handleSubmit}>
  <input name="email" />
  <button type="submit">Submit</button>
</form>
```

### Key Points

| Behavior | Description |
|----------|-------------|
| `event.preventDefault()` | Stops the default browser behavior (page reload/navigation) |
| Triggers | Submit button clicked, or Enter pressed in an input field |
| Accessing data | Use `event.target.elements` or controlled state |

### Button Types in Forms

In HTML forms, **all buttons default to `type="submit"`**. If you have a button that should NOT submit the form, you must explicitly set `type="button"`.

```jsx
<form onSubmit={handleSubmit}>
  <button type="button" onClick={handleCancel}>
    Cancel
  </button>
  <button type="submit">
    Save
  </button>
</form>
```

> ⚠️ **Common bug:** Without `type="button"` on the Cancel button, clicking it will submit the form instead of executing the `onClick` handler.

---

## The `<pre>` Element

The `<pre>` HTML element represents **preformatted text** — content presented exactly as written in the HTML file. The text is typically rendered using a monospaced font.

```jsx
<pre>
  Line 1
    Indented line 2
  Line 3
</pre>
```

Use `<pre>` when whitespace and line breaks are meaningful (e.g., code snippets, ASCII art).
