# JavaScript Essentials

## Declarative vs Imperative

- **Declarative (React):** Describe _what_ you want.
- **Imperative (JS):** Describe _how_ to do it step by step.

## Short-Circuit Evaluation

`A && B` evaluates A; if A is truthy it returns B, otherwise it returns A.
No booleans. Just returning operands.

C/C# treat logical operators as **strict boolean operators** with type constraints.

JavaScript has a concept of truthy and falsy, not strict booleans.

## JS Runtime Environments

### Browser

- Provides `window`, `document`, `localStorage`, etc.
- These are **not importable modules** - they're environment objects automatically available in browsers

### Node.js

- Runs on the server.
- No DOM or browser APIs.
- DOM simulation (fake browser environments) via **jsdom** or **happy-dom**.

## Running JavaScript

```bash
node app.js
```

## Script Tags

```html
<script src="app.js" defer></script>
```

- The tag placement affects execution timing.
- `defer` ensures DOM (HTML structure) loads first before script execution.
- **Alternative**: put script at end of `<body>`.

## Equality Operators

| Operator | Meaning                                                        | Example             |
| -------- | -------------------------------------------------------------- | ------------------- |
| `=`      | assignment                                                     | `x = 1`             |
| `==`     | loose equality (type coercion, compares after type conversion) | `5 == "5"` → true   |
| `===`    | strict equality (compares both value and type)                 | `5 === "5"` → false |

**Tip:** always use `===` to avoid unexpected behavior.

## Nullish Coalescing Operator

The `??` operator returns the right operand when the left operand is `null` or `undefined`, otherwise returns the left operand.

```javascript
const value = null ?? 'default';     // 'default'
const value = undefined ?? 'default'; // 'default'
const value = 0 ?? 'default';        // 0
const value = '' ?? 'default';       // ''
const value = false ?? 'default';    // false
```

**Use cases:**

```javascript
// ✓ Good - preserves falsy values like 0, '', false
const count = userInput ?? 10;  // If userInput is 0, count = 0

// ✗ Bad - treats 0 as missing
const count = userInput || 10;  // If userInput is 0, count = 10

// Common with refs and optional values
const element = ref.current ?? document.body;
```

**Difference from ternary `? :`:**
- `??` only checks for `null` or `undefined`
- `? :` checks for any falsy value (0, '', false, null, undefined)

## Type Conversion

- `+` before a variable converts it to a number.

## Method Calls Need Parentheses

**Common mistake: `.trim` vs `.trim()`**

Always include parentheses `()` when calling methods:

```js
// ❌ WRONG - checks the length of the function itself
if (title.trim.length === 0) {
    alert("Title is required!");
}
// .trim without () is just a reference to the function
// Functions have a .length property (number of parameters), not the trimmed string

// ✅ CORRECT - calls trim() and checks the trimmed string length
if (title.trim().length === 0) {
    alert("Title is required!");
}
```

**The rule:** Method calls need `()`. Without parentheses, you're referencing the function itself, not executing it.

## Arrow Function Syntax Patterns

### Shortcuts

- Single param → parentheses optional
- Single return → braces optional
- Returning object → wrap in `()`

```js
(num) => num * 3;
(num) => ({ value: num });
```

### Arrow Functions: Implicit vs Explicit Return

**Critical:** When using `.map()` with arrow functions, you must understand return behavior:

**Implicit Return** (no curly braces) - automatically returns the expression:
```jsx
items.map((item) => <div key={item}>{item}</div>)
//                  ☝️ Directly returns this
```

**Explicit Return** (with curly braces) - requires `return` statement:
```jsx
// ❌ WRONG - No return, renders nothing
items.map((item) => {
  <div key={item}>{item}</div>;
})

// ✅ CORRECT - Explicit return
items.map((item) => {
  return <div key={item}>{item}</div>;
})

// ✅ ALSO CORRECT - Parentheses for implicit return
items.map((item) => (
  <div key={item}>{item}</div>
))
```

### One-line rule 🧠

> **`()` → implicit return**
> **`{}` → must write `return`**

## Classes

- Properties auto-created when assigned to `this`.
- Use default parameters instead of constructor overloading.
- **Constructor must be named `constructor`** - not the class name

## Functions as Values

Functions can be:

- stored in variables
- passed as arguments
- returned from functions (closure: returned function "remembers" variables from where it was created)

```javascript
const greet = function (name) {
  return `Hello ${name}`;
}; // Function expression
const sayHi = greet; // Assign to another variable
sayHi("John"); // 'Hello John'

// Pass as argument
setTimeout(() => console.log("Done"), 1000);
[1, 2, 3].map((n) => n * 2); // Functions passed to array methods

// Return from function (closure)
const makeMultiplier = (factor) => (n) => n * factor;
const double = makeMultiplier(2);
double(5); // 10
```

## const: Primitives vs Reference

**Primitives** (string, number, boolean): `const` makes the value unchangeable

```javascript
const name = "John";
name = "Jane"; // ✗ Error: can't reassign
```

**Reference types** (objects, arrays): reference fixed, contents mutable

```javascript
const person = { name: "John" };
person.name = "Jane"; // ✓ Works - modifying property
person = {}; // ✗ Error: can't reassign variable

const arr = [1, 2, 3];
arr.push(4); // ✓ Works - modifying array
arr = []; // ✗ Error: can't reassign variable
```

**Why?** `const` locks the variable to point to the same memory address, but the contents at that address can still change.

## Error Handling

- `return Error(...)` - Returns error as a value, code continues
- `throw Error(...)` - Stops code execution, must be caught with try/catch

(The `new` keyword is optional specifically for _Error and its subtypes_ (like `TypeError`, `RangeError`, etc.).)

## setInterval

Repeatedly executes a function at fixed time intervals until stopped.

```javascript
// Basic syntax
const intervalId = setInterval(callback, delayInMs);

// Example: Log every second
const id = setInterval(() => {
  console.log('Tick');
}, 1000);

// Stop the interval
clearInterval(id);
```

**Key points:**
- Returns an interval ID (number) used to stop it later
- Continues indefinitely until `clearInterval(id)` is called
- First execution happens after the delay, not immediately
- **Always store the ID** so you can clear it (prevent memory leaks)

**When to use:**
- Polling data, background saves, simple timers/countdowns
- Tasks where "roughly every N seconds" is acceptable

**When NOT to use:**
- Frame-perfect animations → use `requestAnimationFrame`
- One-time delay → use `setTimeout`

**Background tab throttling:**
Browsers slow down intervals in hidden/background tabs (to save battery/CPU). A 1-second interval may fire every 2–3+ seconds when the tab is not visible. This is why download countdown timers on websites appear to pause or slow when you switch tabs.

**Workarounds (cannot fully prevent, but can handle it):**
- **Use `Date.now()`** — check actual elapsed time instead of trusting interval count
- **Web Workers** — timers in workers are less/not throttled
- **Visibility API** — detect when tab is visible again and recalculate

```javascript
// Check real elapsed time
const startTime = Date.now();
setInterval(() => {
  const elapsed = Math.floor((Date.now() - startTime) / 1000);
  console.log("Actual seconds:", elapsed);
}, 1000);
```

**Common React pattern with useRef:**

```jsx
function Timer() {
  const intervalRef = useRef(null);
  const [seconds, setSeconds] = useState(0);

  const start = () => {
    intervalRef.current = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);
  };

  const stop = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <>
      <p>{seconds}s</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </>
  );
}
```

**⚠️ Important:** Always clear intervals when components unmount to prevent memory leaks and unexpected behavior.
