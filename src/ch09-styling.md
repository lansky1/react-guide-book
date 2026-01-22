# Styling React Projects

## Vanilla CSS

- **Global Scope**: Styles are not scoped to components. Importing a CSS file anywhere in your application applies the styles globally because bundlers inject them into the document head.

## Inline Styles

- **Object Syntax**: Inline styles must be passed as an object to the `style` prop.
- **CamelCase Properties**: CSS properties with hyphens (like `text-align`) must be converted to camelCase (e.g., `textAlign`) or wrapped in quotes (`'text-align'`).
- **Dynamic Styling**: Inline styles are excellent for applying dynamic styles based on state.

```jsx
style={{
  color: titleColour
}}
```

## Scoping styles with CSS Modules

**Build tool feature** (Vite/Webpack) that generates unique class names to scope styles to components.

**Setup:**
1. Name file with `.module.css` extension
2. Import as object: `import styles from './Header.module.css'`
3. Access via object properties: `className={styles.title}`

**How it works:**

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

**Key points:**
- Classes (`.name`) and IDs (`#name`) both transform to unique strings
- Pseudo-classes/descendants: base selector transforms, relationship preserved
- Combine with global styles: `className={`${styles.button} btn-primary`}`
- Not scoped at runtime - unique names generated at build time
- **Generated names are deterministic** - same file/class produces same hash every build (ensures browser caching works)

## CSS-in-JS Styling with Styled Components

Write CSS in JavaScript using tagged template literals. Styles are scoped to components.

**Basic Usage:**
```jsx
import styled from 'styled-components';

const Button = styled.button`
  background: blue;
  color: white;
  padding: 10px 20px;
`;

<Button>Click me</Button>
```

**Nested Selectors:**
```jsx
const Card = styled.div`
  padding: 20px;
  
  &:hover { box-shadow: 0 4px 8px rgba(0,0,0,0.1); }  /* & = self */
  & h2 { color: blue; }                               /* child elements */
  &::before { content: '→'; }                         /* pseudo-elements */
`;
```

**Dynamic Styling with Props:**
```jsx
const Button = styled.button`
  background: ${props => props.$primary ? 'blue' : 'gray'};
`;

<Button $primary>Click</Button>
```

**Note:** Use `$` prefix for transient props - so that they dont clash with the inbuilt props by having the same name.

**Media Queries:**
```jsx
const Container = styled.div`
  width: 100%;
  
  @media (min-width: 768px) {
    width: 750px;
  }
`;
```

**Props automatically flow to underlying HTML elements:**
```jsx
const Input = styled.input`
  border: 1px solid gray;
`;

<Input type="email" placeholder="Enter email" value={email} onChange={handleChange} />
```

## Tailwind CSS

Utility-first CSS framework - style elements using predefined class names directly in JSX.

**Setup:**
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Basic Usage:**
```jsx
<button className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
  Click me
</button>
```

**Key Features:**

- **Utility classes**: Each class applies one specific style (`bg-blue-500` = blue background, `px-4` = horizontal padding)
- **Responsive**: Add breakpoint prefixes (`md:`, `lg:`) for responsive design
- **States**: Prefix for hover/focus/active states (`hover:bg-blue-600`)
- **No CSS files**: All styling in className attributes

**Examples:**

```jsx
// Responsive design
<div className="w-full md:w-1/2 lg:w-1/3">
  Responsive width
</div>

// State variants
<button className="bg-blue-500 hover:bg-blue-700 active:bg-blue-900">
  Interactive
</button>

// Dynamic classes (use template literals)
<div className={`p-4 ${isActive ? 'bg-green-500' : 'bg-gray-500'}`}>
  Conditional styling
</div>
```
