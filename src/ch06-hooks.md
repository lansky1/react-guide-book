# Hooks

## What are Hooks?

All functions starting with `use` (like `useState`, `useEffect`, `useContext`) are React hooks.

## Rules of Hooks

- Only call at top level
- Only inside React components or custom hooks
- Never inside `if`, loops, nested functions

## useState

```js
const [value, setValue] = useState(initial);
```

### State is a Snapshot

When you call `setValue`, React doesn't update `value` immediately. It schedules a re-render with the new value. The current render keeps using the old "snapshot":

```js
// count = 0
console.log(count);      // 0
setCount(count + 1);     // schedules re-render with 1
console.log(count);      // still 0 (snapshot doesn't change mid-render)
```

### Where to Call setState

| Location | Safe? | Why |
| -------- | ----- | --- |
| Event handlers (`onClick`, `onChange`) | ✅ | Runs once per user action |
| `useEffect` | ✅ | Runs after render completes |
| Async callbacks (timers, fetch) | ✅ | Runs later, not during render |
| Component body (directly) | ⚠️ | Runs during render; Causes infinite re-render loops |

```jsx
// ❌ Infinite loop - runs every render, triggers new render
setCount(count + 1);

// ⚠️ Works but anti-pattern - causes 5 unnecessary re-renders
if (count < 5) setCount(count + 1);

// ✅ Use useEffect for side effects (runs after render, not during)
useEffect(() => {
  if (count < 5) setCount(count + 1);
}, [count]);
```

### Functional Updates

Pass a function to get the latest state (not the snapshot):

```jsx
// ❌ All use same snapshot (count = 0) → result is 1
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);

// ✅ Each gets latest value → result is 3
setCount(c => c + 1);
setCount(c => c + 1);
setCount(c => c + 1);
```

Use functional updates when the new state depends on the previous state:

```jsx
setValue(prev => (prev === "X" ? "O" : "X"));
```

**When to use each approach:**

- Pass a **function** when the new state depends on the previous state
- Pass a **value** when the new state is independent of previous state

```jsx
// ✅ Function - depends on previous state
setCount(prev => prev + 1);
setItems(prev => [...prev, newItem]);

// ✅ Value - independent, replacing entirely
setName("John");
setIsOpen(true);
setItems([1, 2, 3]);  // Complete replacement
```

See [Appendix](./ch11-appendix.md) for how state batching works.

## useRef

`useRef` is like a box where you can store a value that React remembers between re-renders, but changing it won't cause a re-render.

### Basic Usage

```js
const ref = useRef(initialValue);
// Creates a box: { current: initialValue }
// Access the value: ref.current
```

### Common Use Cases

#### 1. Accessing DOM Elements

Like `document.querySelector` but the React way:

```jsx
function TextInput() {
  const inputRef = useRef(null);  // Create empty box
  
  const focusInput = () => {
    inputRef.current.focus();  // Access the actual <input> element
  };
  
  return (
    <>
      <input ref={inputRef} type="text" />  {/* Put <input> in the box */}
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}
```

#### 2. Storing Values Without Re-renders

```jsx
function Timer() {
  const timerIdRef = useRef(null);  // Store timer ID
  
  const startTimer = () => {
    timerIdRef.current = setInterval(() => console.log('Tick'), 1000);
  };
  
  const stopTimer = () => {
    clearInterval(timerIdRef.current);  // Use stored ID to stop timer
  };
  
  return (
    <>
      <button onClick={startTimer}>Start</button>
      <button onClick={stopTimer}>Stop</button>
    </>
  );
}
```

### useRef vs useState

| What happens when you change it?  | `useRef`                       | `useState`                         |
| --------------------------------- | ------------------------------ | ---------------------------------- |
| Component re-renders?             | ❌ No                           | ✅ Yes                              |
| Value remembered between renders? | ✅ Yes                          | ✅ Yes                              |
| How to access?                    | `ref.current`                  | `value` (direct)                   |
| Use when?                         | DOM access, storing IDs/timers | UI data that should show on screen |

**Mental Model:** Think of `useRef` as a sticky note that React keeps for you. You can change what's written on it anytime without React caring. With `useState`, changing the value is like shouting "Hey React, update the screen!"

### Best Practices

> ⚠️ **Use refs sparingly!** Refs are an "escape hatch" — don't use them for everything. They're meant for specific cases where React's declarative approach doesn't fit.

#### ❌ Anti-pattern: Direct DOM Manipulation

```jsx
// BAD - Imperative DOM manipulation (breaks React's model)
const divRef = useRef(null);
divRef.current.textContent = "New text";  // ✗ Don't do this!
divRef.current.style.color = "red";       // ✗ React can't track this!
```

#### ✅ Use State for UI Changes

```jsx
// GOOD - Declarative (React's way)
const [text, setText] = useState("Hello");
const [color, setColor] = useState("blue");

<div style={{ color }}>{text}</div>
```

#### When to Use Refs

- Accessing DOM methods (`.focus()`, `.play()`, `.scrollIntoView()`)
- Storing values that don't affect rendering (timer IDs, previous values, element measurements)
- Integrating with non-React libraries that need direct DOM access

**Rule of thumb:** If it affects what the user sees, use `useState`. If it's behind-the-scenes bookkeeping, use `useRef`.

### Module-Level Variables vs Refs

Variables declared outside a component (at module level) are **shared across all instances** of that component. This causes bugs when multiple instances exist on the same page.

#### ❌ The Problem: Shared State

```jsx
let timerId; // Declared outside the component

function Timer({ label }) {
  const start = () => {
    timerId = setInterval(() => console.log(label), 1000);
  };
  const stop = () => {
    clearInterval(timerId);
  };
  return (
    <>
      <span>{label}</span>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </>
  );
}

// Usage: Two timers on the same page
<Timer label="Timer A" />
<Timer label="Timer B" />
```

**What goes wrong:**
1. User clicks "Start" on Timer A → `timerId` stores Timer A's interval ID
2. User clicks "Start" on Timer B → `timerId` is **overwritten** with Timer B's ID
3. User clicks "Stop" on Timer A → Clears Timer B's interval (wrong one!)
4. Timer A keeps running forever, Timer B is stopped

Both components point to the same `timerId` variable in memory.

#### ✅ The Solution: useRef

```jsx
function Timer({ label }) {
  const timerId = useRef(null); // Each instance gets its own ref

  const start = () => {
    timerId.current = setInterval(() => console.log(label), 1000);
  };
  const stop = () => {
    clearInterval(timerId.current);
  };
  return (
    <>
      <span>{label}</span>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </>
  );
}
```

Now each `<Timer />` instance has its **own** `timerId` ref. Starting or stopping one timer doesn't affect the other.

### Passing Refs as Props

#### React 19+

Refs can be passed as regular props:

```jsx
// Parent component
function TimerChallenge() {
  const dialog = useRef();
  return <ResultModal ref={dialog} />;
}

// Child component - ref received as regular prop
function ResultModal({ ref, result }) {
  return <dialog ref={ref}>...</dialog>;
}
```

> ⚠️ **Common Mistake:** Destructure `ref` from props — don't use the old `forwardRef` signature `(props, ref)`.

#### React 18 and Earlier

Required `forwardRef` wrapper:

```jsx
import { forwardRef } from 'react';

// Child component must be wrapped with forwardRef
const ResultModal = forwardRef(({ result }, ref) => {
  return <dialog ref={ref}>...</dialog>;
});

// Parent usage remains the same
function TimerChallenge() {
  const dialog = useRef();
  return <ResultModal ref={dialog} />;
}
```

**Key difference:** React 19 treats `ref` like any other prop, eliminating the need for the special `forwardRef` wrapper.

## useImperativeHandle

### The Problem: Exposing Too Much

When you pass a ref to a child component and attach it to a DOM element, the parent gets **full access** to that DOM node:

```jsx
// Child component
function Modal({ ref }) {
  return <dialog ref={ref}>Secret content</dialog>;
}

// Parent component
function App() {
  const modalRef = useRef();

  const doStuff = () => {
    // Parent can do ANYTHING to the dialog:
    modalRef.current.showModal();           // ✓ Intended
    modalRef.current.close();               // ✓ Maybe intended
    modalRef.current.remove();              // ✗ Dangerous!
    modalRef.current.innerHTML = "Hacked";  // ✗ Breaks React!
  };

  return <Modal ref={modalRef} />;
}
```

This breaks **encapsulation** — the child component has no control over how the parent uses its internals.

### The Solution: useImperativeHandle

`useImperativeHandle` lets the child define **exactly** what the parent can access:

```jsx
import { useRef, useImperativeHandle } from 'react';

function Modal({ ref }) {
  const dialogRef = useRef(); // Internal ref (child keeps this private)

  // Define what the parent's ref can access
  useImperativeHandle(ref, () => ({
    open() {
      dialogRef.current.showModal();
    },
    close() {
      dialogRef.current.close();
    }
    // That's it - no .remove(), no .innerHTML, nothing else
  }));

  return <dialog ref={dialogRef}>Secret content</dialog>;
}
```

Now the parent can only use the methods you explicitly exposed:

```jsx
function App() {
  const modalRef = useRef();

  const handleClick = () => {
    modalRef.current.open();   // ✓ Works
    modalRef.current.close();  // ✓ Works
    modalRef.current.remove(); // ✗ TypeError: not a function
  };

  return <Modal ref={modalRef} />;
}
```

### How It Works

| Without useImperativeHandle | With useImperativeHandle |
| --------------------------- | ------------------------ |
| `ref.current` = the actual DOM node | `ref.current` = custom object you define |
| Parent can call any DOM method | Parent can only call methods you expose |
| Child has no control | Child controls the "API" |

### Mental Model

Think of it like a **vending machine**:

- **Without useImperativeHandle:** You give customers the keys to the stockroom. They can take anything, rearrange items, or break things.
- **With useImperativeHandle:** Customers only have buttons on the front panel. They can select items through the interface you designed, but can't access the internals.

### Complete Example

```jsx
function VideoPlayer({ ref }) {
  const videoRef = useRef();

  useImperativeHandle(ref, () => ({
    play() {
      videoRef.current.play();
    },
    pause() {
      videoRef.current.pause();
    },
    restart() {
      videoRef.current.currentTime = 0;
      videoRef.current.play();
    }
    // Parent cannot: change src, adjust volume, access duration, etc.
  }));

  return <video ref={videoRef} src="/movie.mp4" />;
}

// Parent only sees: { play, pause, restart }
function App() {
  const playerRef = useRef();
  return (
    <>
      <VideoPlayer ref={playerRef} />
      <button onClick={() => playerRef.current.play()}>Play</button>
      <button onClick={() => playerRef.current.pause()}>Pause</button>
      <button onClick={() => playerRef.current.restart()}>Restart</button>
    </>
  );
}
```
