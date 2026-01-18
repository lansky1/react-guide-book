# Practical Lessons

## Form Button Types

**Important:** In HTML forms, all buttons default to `type="submit"` behavior. If you have a button that should NOT submit the form, you **must explicitly set `type="button"`**.

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

Without `type="button"` on the Cancel button, clicking it will submit the form instead of executing the `onClick` handler. This is a common source of bugs.

## Classic JavaScript Practice

**Lessons Learned:**

1. Opened an HTML file locally
2. Import statements don't work in `app.js` - only the loaded script will work
3. Since HTML renders after script, and script was in `<head>`, had to use `defer` attribute

## ES Modules Practice

**Lessons Learned:**

1. ES Modules are a **browser/JavaScript feature**, not React-specific
2. `type="module"` is required by browsers to support `import/export` syntax
3. CORS restrictions apply (requires `http://` server, not `file://`)
4. Unable to open local HTML file directly - must use a development server
