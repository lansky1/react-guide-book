# Development Tools

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
