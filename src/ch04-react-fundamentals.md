# React Fundamentals

## JSX - HTML-like syntax sugar

In JSX curly braces `{}`, you can only use **expressions** (code that returns a value), not **statements**.

**Allowed (expressions):**

```jsx
{
  5 + 3;
} // Math operations
{
  user.name;
} // Property access
{
  getName();
} // Function calls
{
  isActive ? "Yes" : "No";
} // Ternary operator
{
  isActive && <div>Active</div>;
} // Logical AND
{
  items.map((item) => <li key={item.id}>{item}</li>);
} // Array methods
```

**Not allowed (statements):**

```jsx
{if (condition) { ... }}         // ✗ if statements
{for (let i = 0; i < 5; i++) { ... }}  // ✗ for loops
{const x = 5;}                   // ✗ Variable declarations
```

**Workarounds:**

```jsx
// Instead of if/else - use ternary or logical operators
{
  condition ? <div>True</div> : <div>False</div>;
}
{
  condition && <div>Show this</div>;
}

// Instead of loops - use array methods
{
  items.map((item) => <div key={item.id}>{item.name}</div>);
}

// For complex logic - extract to a function
{
  getContent();
}
```

**Rule:** If it returns a value, it's an expression. If it doesn't return a value, it's a statement.

**Attribute Naming:**
- `class` and `for` are reserved words in JavaScript.
- Use `className` instead of `class`.
- Use `htmlFor` instead of `for`.

**Attribute Values:**
- **String values**: Use quotes directly: `title="Easy"`
- **Non-string values** (numbers, booleans, variables, objects, etc.): Use curly braces: `targetTime={1}`, `isActive={true}`, `data={user}`
- Without curly braces, values are treated as strings: `targetTime="1"` passes the string `"1"`, not the number `1`

## Props

Props is always an object in React. When defining a component function, you receive props as a single object parameter, not as individual arguments.

```jsx
// ✓ Correct - props is an object
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// ✓ Also correct - destructure the props object
function Welcome({ name, age }) {
  return <h1>Hello, {name}</h1>;
}

// ✗ Wrong - cannot pass attributes as separate parameters
function Welcome(name, age) {
  // This won't work
  return <h1>Hello, {name}</h1>;
}
```

**Key points:**

- React bundles all attributes into a single props object: `{ name: "John", age: 30 }`
- Component receives this object as its first parameter
- Use destructuring for cleaner access to individual props
- **Props are not automatically forwarded** to inner elements - you must explicitly pass them down

```jsx
function SearchInput(props) {
  // ✗ Props don't automatically apply to <input>
  return <input />;

  // ✓ Spread props to forward everything
  return <input type="search" {...props} />;
}

<SearchInput
  placeholder="Search..."
  value={query}
  onChange={handleSearch}
  className="search-box"
/>;
```

**Accessing Props**

- **Before `return`** (plain JavaScript): Use variables directly → `name`, `age`
- **Inside JSX** (within `return (...)`): Wrap in `{}` → `{name}`, `{age}`

```jsx
function Profile({ name, age }) {
  // Before return: plain JS, no {} needed
  const greeting = "Hello " + name;

  return (
    // Inside JSX: need {} to embed JS
    <p>{greeting} - {age} years old</p>
  );
}
```

## children Prop

The `children` prop contains any content placed between component opening and closing tags.

```jsx
<Component>Inner content</Component>
```

Becomes:

```js
props.children;
```

**Component Composition:** The `children` prop enables component composition - building complex UIs by nesting components inside each other, making components flexible and reusable.

```jsx
<Card>
  <Header />
  <Content />
  <Footer />
</Card>
```

**Multiple Insertion Points (Slots):**

Use custom props (not just `children`) to create multiple "slots" for JSX content:

```jsx
function Card({ header, children, footer }) {
  return (
    <div className="card">
      <div className="header">{header}</div>
      <div className="body">{children}</div>
      <div className="footer">{footer}</div>
    </div>
  );
}

// Usage - inject JSX into 3 different slots
<Card
  header={<h2>Title</h2>}
  footer={<button>Close</button>}
>
  <p>Main content goes here</p>  {/* children slot */}
</Card>
```

**Key points:**

- JSX can be passed as props just like any other data
- Multiple named props allow injecting content into different locations
- This pattern enables flexible component composition with multiple insertion points

## Dynamic Component Types

You can pass component identifiers as props to render different wrapper elements dynamically.

**Two types of identifiers:**

| Type                 | Passed As            | Example                         |
| -------------------- | -------------------- | ------------------------------- |
| **Built-in HTML**    | String               | `"menu"`, `"div"`, `"ul"`       |
| **Custom Component** | Identifier (not JSX) | `{Section}` (NOT `<Section />`) |

```jsx
// Built-in element as string
<Tabs operation="menu" content={...} />

// Custom component as identifier
<Tabs operation={Section} content={...} />
```

**Implementation:**

```jsx
function Tabs({ operation = "menu", content }) {
  // ✓ MUST use uppercase - React requires it for dynamic components
  const Operation = operation;

  return <Operation>{content}</Operation>;
}
```

**Critical Rules:**

1. **Component identifiers MUST start with uppercase in JSX**

   - `<Operation>` ✓ Works (React treats as component)
   - `<operation>` ✗ Fails (React treats as HTML tag like `<div>`)

2. **Built-in elements use strings, custom components use identifiers**
   - Built-in: `operation="div"`
   - Custom: `operation={MyComponent}`

**Why uppercase?** React uses naming convention to distinguish:

- Lowercase = built-in HTML element
- Uppercase = React component to resolve

## JSX Fragments

React components must return a **single root element**. When you need multiple sibling elements, wrap them in a fragment.

```jsx
return (
  <>
    <div>First</div>
    <div>Second</div>
  </>
);

// ✓ Alternative explicit syntax
return (
  <React.Fragment>
    <div>First</div>
    <div>Second</div>
  </React.Fragment>
);
```

**Why fragments?** They group elements without adding extra DOM nodes (unlike wrapping in a `<div>`).

## Conditional Rendering

**Quick:** Use expressions in JSX. Prefer the **ternary** for two branches and `&&` for simple "render or not" cases.

```jsx
// two branches
{
  isLoggedIn ? <Dashboard /> : <SignIn />;
}

// render only when truthy
{
  isLoading && <Spinner />;
}

// render nothing
{
  showBanner ? <Banner /> : null;
}

// classes
<button className={isActive ? "btn-active" : "btn"}>Click</button>;
```

**Best practices:**

- Ternary for clear two-way choices; `&&` for single conditional renders.
- Avoid nested ternaries — extract logic to a variable or component.
- Ensure expressions return a React node or `null`.

## React Immutability

- React requires immutability for **change detection**
- Mutating state directly breaks React's rendering

```javascript
const [items, setItems] = useState([1, 2, 3]);

// ❌ BAD - Mutates the same array reference
items.push(4);
setItems(items); // React sees same reference, won't re-render!

// ✅ GOOD - Creates new array reference
setItems([...items, 4]); // React sees new reference, re-renders!
```

**How React Detects Changes:**

React uses **shallow comparison** (reference equality):

```javascript
oldArray === newArray; // If false, re-render
```

When you use `.push()`, the array reference stays the same, so React thinks nothing changed and won't re-render your component.

## Array Update Patterns in React

In React, you should **never** use `.push()`, `.pop()`, `.shift()`, `.unshift()`, `.splice()`, or any other mutating array methods directly on state.

**Use these non-mutating alternatives:**

| Task              | ❌ Don't Use         | ✅ Use Instead                                        |
| ----------------- | ------------------- | ---------------------------------------------------- |
| Add to end        | `arr.push(item)`    | `[...arr, item]`                                     |
| Add to start      | `arr.unshift(item)` | `[item, ...arr]`                                     |
| Remove from end   | `arr.pop()`         | `arr.slice(0, -1)`                                   |
| Remove from start | `arr.shift()`       | `arr.slice(1)`                                       |
| Remove by index   | `arr.splice(i, 1)`  | `arr.filter((_, idx) => idx !== i)`                  |
| Replace item      | `arr[i] = newItem`  | `arr.map((item, idx) => idx === i ? newItem : item)` |
| Sort              | `arr.sort()`        | `[...arr].sort()`                                    |
| Reverse           | `arr.reverse()`     | `[...arr].reverse()`                                 |

**The rule:** Always create a **new array** when updating state. The spread operator `...` is your best friend for this.

## Rendering Arrays

JSX auto-concatenates:

```jsx
<div>{["a", "b"]}</div>
// → "ab"
```

JavaScript:

```jsx
console.log(items.toString()); // "apple,banana,orange" (with commas)
```

**Proper ways to render arrays:**

```jsx
// Render as list elements
{
  items.map((item) => <div key={item}>{item}</div>);
}

// Render with separators
{
  items.join(", ");
} // "apple, banana, orange"
```

**Why?** JSX is designed for rendering lists of React elements. When given an array, it concatenates all elements for rendering convenience.

**The `.map()` method provides two parameters automatically:**

```jsx
array.map((item, index) => ...)
//         ^^^^  ^^^^^
//         1st   2nd parameter
```

- First parameter: the current item in the array
- Second parameter: the index/position of that item

The `index` is useful for setting the `key` prop. However, using `index` as a key isn't ideal if you'll be reordering, filtering, or deleting items. For simple lists where items are only added to the end, `index` works fine.

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

## Import Conventions

**File extensions**: React conventionally omits file extensions in import statements

```javascript
// React style (preferred)
import Component from "./Component";

// Not typically used
import Component from "./Component.jsx";
```

## CSS Scoping in React

**Important:** CSS files imported in React components are **NOT scoped** to that component - they are global!

```javascript
import "./Header.css"; // ✗ This applies globally to entire app
```

**Why?** When you import a CSS file, the bundler (Webpack/Vite) injects it into the global stylesheet. The import location is just for organization - the styles affect the entire application.

**Example:**

```css
/* Header.css */
header {
  color: blue;
}
```

```javascript
// Header.jsx
import "./Header.css"; // Importing here doesn't scope it!
```

This will style **all** `<header>` elements in your entire app, not just the Header component.

**Comparison with Angular:**

| Framework   | Default Behavior                                      |
| ----------- | ----------------------------------------------------- |
| **Angular** | Styles scoped by default (ViewEncapsulation.Emulated) |
| **React**   | Styles are global by default                          |

Angular automatically adds unique attributes like `_ngcontent-abc-123` to scope styles. React has no such mechanism by default.

## Forms & Two-Way Binding

**Controlled (two-way binding):** React state controls the input.

```jsx
const [name, setName] = useState('');
<input value={name} onChange={(e) => setName(e.target.value)} />
```

- `value` displays state → `onChange` updates state → loop
- Without `onChange`, input is frozen (can't type)

**Uncontrolled:** Browser manages input, read via ref when needed.

```jsx
const inputRef = useRef();
<input defaultValue="Initial" ref={inputRef} />

// Later: inputRef.current.value
```

- `defaultValue` sets initial value only
- Use for simple forms where you only need value on submit

## Portals

Portals render JSX into a **different DOM location** while staying part of React's component tree.

```jsx
import { createPortal } from 'react-dom';

function Modal({ children, isOpen }) {
  if (!isOpen) return null;
  
  return createPortal(
    <div className="modal">{children}</div>,
    document.getElementById('modal-root')  // Renders HERE, not inside parent
  );
}
```

```html
<!-- index.html -->
<body>
  <div id="root"></div>
  <div id="modal-root"></div>
</body>
```

**Why?** Escape CSS issues—parent's `overflow: hidden`, z-index problems, clipping.

**Event bubbling with portals:**

The modal HTML moves to `modal-root`, but React remembers where you *wrote* it:

```jsx
function Parent() {
  return (
    <div onClick={() => console.log('Caught!')}>
      <Modal isOpen={true}>
        <button>Click</button>  {/* Lives in modal-root in DOM */}
      </Modal>
    </div>
  );
}
```

Clicking the button logs "Caught!" even though the button is outside the div in actual HTML. React routes events through the **component tree you wrote**, not the DOM tree.

*Analogy: Send your kid to grandma's house—you still get the phone call when they misbehave.*

**Use cases:** Modals, tooltips, dropdowns, toasts, overlays.
