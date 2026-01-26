# Module Systems

## Classic JavaScript

- No import/export support
- Requires manual `defer` attribute when needed
- Scripts load in order they appear in HTML
- Import statements don't work — only the loaded script will execute

> 💡 **Tip:** Since HTML renders after the script in `<head>`, use the `defer` attribute or place the script at the end of `<body>`.

## ES Modules

- Enable with:

```html
<script type="module"></script>
```

- Automatically deferred (no need for `defer` attribute)
- Supports import/export statements
- ES Modules are a **browser/JavaScript feature**, not React-specific
- Cannot use over `file://` → must use a dev server

### Default vs Named Exports

**Default Export** (one per file):

```javascript
export default function myFunction() { }
export default 'value';
// Import: import myFunction from './file'  (any name works)
// Note: Can't do 'export default const'. Use: const x = val; export default x;
// Cant have more than one default - variables and function combined
```

**Named Export** (multiple per file):

```javascript
export const name = "value";
export function helper() {}
// Import: import { name, helper } from './file'  (exact names required)
```

## Import Conventions

**File extensions**: React conventionally omits file extensions in import statements

```javascript
// React style (preferred)
import Component from "./Component";

// Not typically used
import Component from "./Component.jsx";
```

## CommonJS

- Older module system used by Node.js before ES Modules
- Uses `require()` and `module.exports`
