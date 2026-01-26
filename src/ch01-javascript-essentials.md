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

**⚠️ Important:** Always clear intervals when components unmount to prevent memory leaks and unexpected behavior. See the [Hooks chapter](./ch06-hooks.md) for React-specific patterns using `useRef` with intervals.

## DOM Manipulation

### Selecting Elements

**`querySelector()`**: Returns the **first** element that matches a CSS selector

```javascript
document.querySelector(".class-name"); // First element with class
document.querySelector("#id"); // Element by ID
document.querySelector("div > p"); // First p inside a div
```

**`querySelectorAll()`**: Returns **all** matching elements as a NodeList

```javascript
document.querySelectorAll(".item"); // All elements with class 'item'
```

> **NodeList**: Array-like but not an array. Has `.forEach()` and `.length`, but lacks `.map()` / `.filter()`. Convert with `Array.from(nodeList)` or `[...nodeList]` if needed.

### Content Properties

**⚠️ XSS Warning:** Never insert user input using `innerHTML`.

- **`innerHTML`**: Use when you need to **read or write HTML tags**

  - Example: `div.innerHTML = '<strong>Bold</strong> text';`

- **`textContent`**: Use when you only need **plain text**. It's fast and safe (reads raw text directly)

  - Example: `div.textContent = 'Just plain text';`

- **`innerText`**: Use only if you need the **visible text** (what the user sees). Slower because it checks CSS, triggers layout, and skips hidden elements

- **`outerHTML`**: Use to **replace the whole element** with new HTML

  - Example: `div.outerHTML = '<p>The div is now a paragraph.</p>';`

- **`outerText`**: When you change it, it **replaces the entire element with just plain text**, destroying the original element

> **Simple rule:** `*HTML` properties = tags are **rendered**. `*Text` properties = tags are shown **as-is** (plain text).

### Security Considerations

> **⚠️ Security Warning:** If you use `innerHTML` with text from a user (like a comment), a hacker could write `<script>alert('hacked!')</script>`. Your webpage would run that script. This is called a **Cross-Site Scripting (XSS)** attack.

## Data Structures

**Maps**: Use `.get(key)` to retrieve values

```javascript
const map = new Map();
map.get(key); // ✓ Correct
map[key]; // ✗ Won't work - returns undefined
```

**Objects**: Use bracket notation or dot notation

```javascript
const obj = { name: "John" };
obj["name"]; // ✓ Works
obj.name; // ✓ Works
```

## Destructuring

Extract values from arrays or objects into individual variables using a concise syntax. Other languages refer to this as `pattern matching`.

**Array Destructuring:**

```javascript
const [first, second] = [1, 2, 3];
// first = 1, second = 2

const [a, , c] = [1, 2, 3]; // Skip elements
// a = 1, c = 3
```

**Object Destructuring:**

```javascript
const { name, age } = { name: "John", age: 30 };
// name = 'John', age = 30

const { name: userName, age } = { name: "John", age: 30 }; // Rename
// userName = 'John', age = 30
```

**Function Parameters:**

```javascript
function greet({ name, age }) {
  console.log(`${name} is ${age}`);
}
greet({ name: "John", age: 30 }); // John is 30

// this is cleaner in handling optional params and for adding new params
```

> **Note:** Parameter names must match object property names. To rename: `{ name: firstName }` extracts `name` as `firstName`.

**Array vs Object Destructuring:**

Arrays are position-based (names can be anything):

```javascript
const [first, second] = [1, 2, 3];
// first = 1, second = 2 (names don't matter)
```

Objects are name-based (names must match properties):

```javascript
const { age, name } = { name: "John", age: 30 };
// age = 30, name = 'John' (order doesn't matter, names must match)

const { x } = { name: "John" };
// x = undefined (no property named 'x')
```

## Spread Operator

Copy or combine arrays/objects using `...` syntax.

**Arrays:**

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4]
const copy = [...arr1]; // [1, 2]
```

**Objects:**

```javascript
const obj1 = { name: "John" };
const obj2 = { age: 30 };
const merged = { ...obj1, ...obj2 }; // { name: 'John', age: 30 }
```

**vs `concat()`:** Spread is more flexible (insert anywhere), concat always appends to end.

**In JSX (spreading props):**

```jsx
const props = { title: "Components", description: "...", image: "..." };

// With spread - unpacks object into individual props
<CoreConcept {...props} />
// Equivalent to:
<CoreConcept title="Components" description="..." image="..." />

// Without spread - passes entire object as single prop
<CoreConcept concept={props} />
// Component must access: concept.title, concept.description, etc.
```

**Analogy:** Think of it like unpacking a box:

- **Without `...`**: You hand someone the entire box
- **With `...`**: You unpack the box and hand them each item separately

### Spread Operator with Computed Property Names

**Pattern:**
```javascript
setPlayers(prevPlayer => {
  return {
    ...prevPlayer,
    [symbol]: newName
  }
});
```

**How it works:**

1. **`...prevPlayer`** - Spread operator copies all properties from the previous state object into a new object
   ```javascript
   prevPlayer = { X: "Player 1", O: "Player 2" }
   { ...prevPlayer } // Creates NEW object: { X: "Player 1", O: "Player 2" }
   ```

2. **`[symbol]: newName`** - Computed property name (brackets make it dynamic)
   - If `symbol = "X"` and `newName = "Alice"`, this becomes `X: "Alice"`
   - If `symbol = "O"` and `newName = "Bob"`, this becomes `O: "Bob"`

3. **Last property wins** - When you have duplicate keys, the last one overwrites:
   ```javascript
   {
     X: "Player 1",  // From spread
     O: "Player 2",  // From spread
     X: "Alice"      // Overwrites the X from spread
   }
   // Result: { X: "Alice", O: "Player 2" }
   ```

## Shallow vs Deep Copy

**Shallow Copy** creates a new outer container but keeps references to the same inner objects.

```javascript
// 2D array example
const original = [
  [1, 2, 3],  // Memory address: 0x001
  [4, 5, 6],  // Memory address: 0x002
];

const shallow = [...original];

// What happened:
shallow !== original              // ✓ Different outer arrays
shallow[0] === original[0]        // ✗ SAME inner arrays (still 0x001)
shallow[1] === original[1]        // ✗ SAME inner arrays (still 0x002)

// The problem:
shallow[0][0] = 99;
console.log(original[0][0]);      // 99 - Original mutated!
```

**Deep Copy** creates new containers at all levels - completely independent copies.

```javascript
// Deep copy with map
const deep = original.map(row => [...row]);

// What happened:
deep !== original                 // ✓ Different outer arrays  
deep[0] !== original[0]           // ✓ Different inner arrays (new address)
deep[1] !== original[1]           // ✓ Different inner arrays (new address)

// Now safe:
deep[0][0] = 99;
console.log(original[0][0]);      // 1 - Original unchanged!
```

**Rule of thumb:**
- **Shallow copy**: Use for flat arrays/objects (one level)
- **Deep copy**: Use for nested structures (arrays of arrays, nested objects)

**Why `.map()` creates a new array:**

The `.map()` method **always returns a new array** - you don't need to spread the result.

**In deep copy context:**

```javascript
// ✓ Correct - .map() creates outer array
const deep = gameBoardState.map(row => [...row]);

// ✗ Redundant - extra spread creates unnecessary copy
const deep = [...gameBoardState.map(row => [...row])];
```

## Rest Operator

Collect remaining elements into a single variable using `...` syntax (opposite of spread).

**With Arrays:**

```javascript
const [first, second, ...rest] = [1, 2, 3, 4, 5];
// first = 1, second = 2, rest = [3, 4, 5]
```

**With Objects:**

```javascript
const { name, age, ...otherInfo } = {
  name: "John",
  age: 30,
  city: "NYC",
  country: "USA",
};
// name = 'John', age = 30
// otherInfo = { city: 'NYC', country: 'USA' }
```

**In Function Parameters:**

```javascript
function sum(first, ...numbers) {
  // first = 1, numbers = [2, 3, 4]
  return first + numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4); // 10
```

**Spread vs Rest:** Same syntax `...`, different purpose:

- **Spread**: Expands an array/object into individual elements
- **Rest**: Collects multiple elements into an array/object
