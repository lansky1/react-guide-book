# Styling React Projects

This chapter covers the various approaches to styling React components, from vanilla CSS to utility frameworks.

---

## Vanilla CSS

Import CSS files directly into your components.

```jsx
import './App.css';
```

> ⚠️ **Global Scope:** Styles are not scoped to components. Importing a CSS file anywhere in your application applies the styles globally because bundlers inject them into the document head.

---

## Inline Styles

Pass styles directly to elements via the `style` prop as a JavaScript object.

```jsx
<h1 style={{ color: titleColour, textAlign: 'center' }}>
  Hello
</h1>
```

### Why CamelCase?

CSS properties like `text-align` contain hyphens, which JavaScript interprets as subtraction:

```javascript
// ✗ Invalid — JS sees: text minus align
{ text-align: 'center' }

// ✓ Valid — camelCase identifier
{ textAlign: 'center' }
```

React uses camelCase to match the DOM API (`element.style.textAlign`).

| Rule | Example |
|------|---------|
| Must be an object | `style={{ ... }}` not `style="..."` |
| CamelCase properties | `textAlign` not `text-align` |
| Values as strings | `fontSize: '16px'` or numbers for pixels |

**Best for:** Dynamic styles based on state or props.

---

## CSS Modules

A **build tool feature** (Vite/Webpack) that generates unique class names to scope styles to components.

### Setup

1. Name file with `.module.css` extension
2. Import as object: `import styles from './Header.module.css'`
3. Access via object properties: `className={styles.title}`

### How It Works

```css
/* Header.module.css */
.title { color: blue; }
#main { background: white; }
```

```jsx
import styles from './Header.module.css';

// styles object: { title: "Header_title__a1b2c3", main: "Header_main__x9y8z7" }

<div id={styles.main} className={styles.title}>Hello</div>
// Renders: <div id="Header_main__x9y8z7" class="Header_title__a1b2c3">Hello</div>
```

### Key Points

| Feature | Behavior |
|---------|----------|
| Selectors | Both classes (`.name`) and IDs (`#name`) transform to unique strings |
| Pseudo-classes | Only the class name changes, the `:hover`/`:focus` part stays (see below) |
| Combining styles | `className={\`${styles.button} btn-primary\`}` |
| Deterministic | Same file/class produces same hash every build (enables browser caching) |

### Pseudo-classes in CSS Modules

When you write pseudo-classes like `:hover` or descendant selectors, CSS Modules only transforms the class name — the relationship stays intact:

```css
/* Input */
.button:hover { background: blue; }
.card .title { font-size: 20px; }

/* Output (after build) */
.Header_button__a1b2c3:hover { background: blue; }
.Header_card__x9y8z7 .Header_title__q5w6e8 { font-size: 20px; }
```

The `:hover` and parent-child relationship are preserved — only the class names become unique.

> 💡 **Note:** Scoping happens at build time via unique names, not at runtime.

---

## Styled Components (CSS-in-JS)

Write CSS in JavaScript using tagged template literals. Styles are scoped to components.

```bash
npm install styled-components
```

### Basic Usage

```jsx
import styled from 'styled-components';

const Button = styled.button`
  background: blue;
  color: white;
  padding: 10px 20px;
`;

<Button>Click me</Button>
```

### Nested Selectors

Use `&` to reference the component itself:

```jsx
const Card = styled.div`
  padding: 20px;
  
  &:hover { box-shadow: 0 4px 8px rgba(0,0,0,0.1); }  /* self */
  & h2 { color: blue; }                               /* children */
  &::before { content: '→'; }                         /* pseudo-elements */
`;
```

### Dynamic Styling with Props

```jsx
const Button = styled.button`
  background: ${props => props.$primary ? 'blue' : 'gray'};
`;

<Button $primary>Click</Button>
```

### Transient Props (`$` prefix)

Styled-components spreads all props onto the underlying DOM element (so `onClick`, `disabled` work). But custom props like `primary` cause React warnings — HTML `<button>` doesn't recognize them.

```jsx
// ✗ Without $: prop reaches the DOM → React warning
<Button primary>       // DOM: <button primary> ❌

// ✓ With $: prop is consumed for styling, not forwarded
<Button $primary>      // DOM: <button> ✓
```

The `$` prefix tells styled-components: "use this for styling only, don't pass to DOM."

### Props Flow Through

Props you pass to a styled component are forwarded to the underlying HTML element:

```jsx
const Input = styled.input`
  border: 1px solid gray;
`;

<Input type="email" placeholder="Enter email" value={email} onChange={handleChange} />
// Renders: <input type="email" placeholder="Enter email" ... />
```

The styled component is just a wrapper — it applies CSS, then passes `type`, `placeholder`, `value`, and `onChange` to the actual `<input>` element. This is why native HTML attributes and event handlers just work.

### Media Queries

```jsx
const Container = styled.div`
  width: 100%;
  
  @media (min-width: 768px) {
    width: 750px;
  }
`;
```

---

## Tailwind CSS

Utility-first CSS framework — style elements using predefined class names directly in JSX.

### Setup

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Basic Usage

```jsx
<button className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
  Click me
</button>
```

### Class Naming Conventions

| Pattern | Example | Meaning |
|---------|---------|---------|
| Property-value | `bg-blue-500` | Blue background (shade 500) |
| Spacing | `px-4`, `py-2`, `m-4` | Padding/margin in rem units |
| Responsive | `md:w-1/2` | Apply at medium breakpoint and up |
| State | `hover:bg-blue-600` | Apply on hover |

### Examples

```jsx
// Responsive design
<div className="w-full md:w-1/2 lg:w-1/3">
  Responsive width
</div>

// State variants
<button className="bg-blue-500 hover:bg-blue-700 active:bg-blue-900">
  Interactive
</button>

// Dynamic classes
<div className={`p-4 ${isActive ? 'bg-green-500' : 'bg-gray-500'}`}>
  Conditional styling
</div>
```
