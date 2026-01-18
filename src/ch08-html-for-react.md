# HTML for React Developers

```text
The <pre> HTML element represents preformatted text which is to be presented exactly as written in the HTML file. The text is typically rendered using a non-proportional, or monospaced font.
```

## Dialog and Modal Elements

**`<dialog>` Element:**
- Native HTML element for dialogs/modals
- **Invisible by default** - requires `open` attribute or `.showModal()` method to display
- Two display modes:
  - `<dialog open>` - displays as regular element in document flow
  - `dialog.showModal()` - displays as modal with backdrop

**Form Integration:**
```jsx
<dialog className="result-modal" open>
  <h2>Dialog Title</h2>
  <form method="dialog">
    <button>Close</button>
  </form>
</dialog>
```

**Key points:**
- `<form method="dialog">` automatically closes the dialog when submitted (no JavaScript needed)
- Button inside form with `method="dialog"` triggers dialog close on click
- Alternative: use refs with `.showModal()` and `.close()` methods for programmatic control

## Form onSubmit

The `onSubmit` prop handles form submission events.

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

**Key points:**
- `event.preventDefault()` stops the default browser behavior (page reload/navigation)
- Fires when: submit button clicked, or Enter pressed in an input field
- Access inputs via `event.target.elements` or controlled state

## Dialog onClose

The `onClose` event fires when a `<dialog>` is closed (via ESC key, `method="dialog"` form, or `.close()` call).

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

**Key points:**
- Fires regardless of *how* the dialog was closed
- Useful for cleanup, resetting state, or triggering side effects
- Does NOT fire if dialog is hidden by removing `open` attribute directly
