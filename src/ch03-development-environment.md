# Development Environment

```
Source Code (JSX, CSS, images)
        │
        ▼
     Bundler (Vite / CRA / Webpack)
        │ transforms & optimizes
        ▼
   Output Bundle (browser-ready)
        │ auto-injected into HTML
        ▼
      Browser Executes App
```

## Build Process

React applications handle scripts differently from traditional web pages:

- React applications typically have **no `<script>` tags in `index.html`**
- The build process automatically injects scripts after transforming your code
- Your source code is compiled, bundled, and optimized before execution

## Importing CSS and Images

In React projects with bundlers (Webpack, Vite, Create React App), you can import non-JavaScript files:

**CSS imports:**

```javascript
import "./styles.css"; // Bundler processes and injects into DOM
```

**Image imports:**

```javascript
import logo from "./logo.png"; // Returns URL string
<img src={logo} alt="Logo" />;
```

## Public Folder vs Source Assets

**`public/` Folder:**
- Resources accessible to all visitors via absolute paths (e.g., `/logo.png`)
- Referenced directly in `index.html` or as `/path/to/file`
- **Not** processed/optimized by bundler
- **Tip:** Truly static data (copyright text, contact email) can go directly in `index.html` instead of using React/JS

**`src/` Folder:**
- Files **must be imported** in code: `import logo from './assets/logo.png'`
- Bundler processes, optimizes, renames (adds hash: `logo.abc123.png`)
- The import gives you the final URL automatically
- **Only works with bundlers** — browsers can't natively `import` images/CSS. Bundlers transform them into JavaScript modules.

> Without a bundler, browsers only `import` JavaScript files, not images/CSS.

## JavaScript Runtimes

- **Node.js**: Traditional JavaScript runtime for server-side development
- **Deno**: Modern alternative to Node.js with enhanced security

## Build Tools

- **react-scripts**: Create React App's build tool (abstraction over webpack)
- **Vite**: Fast, modern build tool with instant HMR (Hot Module Replacement)
- **Parcel**: Zero-configuration bundler

## Local Development Servers

### Node.js (http-server)

```bash
npm install -g http-server
http-server .
```

### Python

```bash
python -m http.server 8000
```

### Deno

```bash
deno run --allow-net --allow-read --unsafely-ignore-certificate-errors https://deno.land/std/http/file_server.ts
```

> **Note**: `0.0.0.0` is localhost

> If there is an installation problem, run with npx http-server .
> A nice alternative to `http-server` is `light-server`. It supports file watching and auto-refreshing and many other features - this requires arguments, need to explore.
