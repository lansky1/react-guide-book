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

## Data Structure

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

## CommonJS

- Older module system used by Node.js before ES Modules
- Uses `require()` and `module.exports`
