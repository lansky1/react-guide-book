# Advanced State Management

## Prop Drilling

**What**: Passing props through multiple component layers to reach a deeply nested child component, even though intermediate components don't use those props.

**Problem**: 
- Makes code harder to maintain and refactor
- Creates unnecessary coupling between components
- Intermediate components become cluttered with props they don't use

**Pattern**:
```jsx
// Parent has state
<Parent data={data}>
  <Middle data={data}>      {/* Just passing through */}
    <Child data={data} />   {/* Actually uses it */}
  </Middle>
</Parent>
```

## Component Composition

**What**: Wrapper components that accept children as props, allowing the parent to determine their content.

**Solution**: Instead of passing props through intermediate components, move child component rendering up to the parent level and use the intermediate component as a wrapper.

**Pattern**:
```jsx
// Before: Shop renders Product internally (prop drilling)
<Shop products={products} onAddToCart={handleAddToCart} />

// After: App renders Product, Shop just provides layout
<Shop>
  {products.map(product => (
    <Product {...product} onAddToCart={handleAddToCart} />
  ))}
</Shop>
```

**Implementation**:
```jsx
// Shop component accepts children instead of props
function Shop({ children }) {
  return <section id="shop">{children}</section>;
}
```

**Benefits**:
- Eliminates prop drilling for deeply nested components
- Shop no longer needs to know about `onAddToCart` or product logic
- More flexible and reusable components

## Context API

**What**: A mechanism for sharing data across components at any nesting depth (component layers) without passing props manually through each level.

**When to Use**: When multiple components need access to the same data (theme, auth, cart state) and prop drilling becomes cumbersome.

### Creating Context

```jsx
// store/shopping-cart-context.jsx
import { createContext } from 'react';

// createContext returns an object containing a React component
// Initial value serves as default when no Provider is found
const CartContext = createContext({
  items: [],
  addItem: () => {}
});

export default CartContext;
```

**Convention**: Store context files in a `store/` folder—a central data store for the app.

### Providing Context

```jsx
// React 19+: Use the context directly as a component
<CartContext value={{ items, addItem }}>
  <App />
</CartContext>

// React 18 and below: Use the .Provider property
<CartContext.Provider value={{ items, addItem }}>
  <App />
</CartContext.Provider>
```

**Why the change?** React 19 simplified the API—the context object itself now acts as the provider, eliminating the need for the `.Provider` wrapper component.

### Consuming Context

**Three ways to consume context**:
- `useContext` — standard hook (React 16.8+)
- `use` — React 19+, more flexible (can be used inside if blocks)
- `.Consumer` — legacy render prop pattern (pre-hooks)

#### Modern Approach: Hooks

```jsx
import { useContext } from "react";
import { CartContext } from "../store/shopping-cart-context";

function Cart() {
  // Option 1: Store in a variable
  const cartCtx = useContext(CartContext);
  
  // Option 2: Destructure directly
  const { items, addItem } = useContext(CartContext);
  
  return <p>{items.length} items</p>;
}
```

#### Legacy Approach: Consumer Component

**Before hooks existed**, you had to use the `.Consumer` property with a render prop pattern:

```jsx
import { CartContext } from "../store/shopping-cart-context";

function Cart() {
  return (
    <CartContext.Consumer>
      {(cartCtx) => {
        return <p>{cartCtx.items.length} items</p>;
      }}
    </CartContext.Consumer>
  );
}
```

**Why it's outdated**: 
- More verbose than `useContext`
- Creates nesting hell with multiple contexts
- Can't be used in class component lifecycle methods

### Default Value vs Provider Value

| Scenario | Result |
|----------|--------|
| No Provider in tree | Default value used ✓ |
| Provider without `value` prop | `undefined` (breaks!) ✗ |
| Provider with `value` prop | That value used ✓ |

### Key Insights

1. **Default Value = Fallback Only**  
   The default value only applies when no Provider exists in the component tree. Once you add a Provider, you must always include the `value` prop.

2. **Provider Enables Reactivity**  
   The Provider's `value` prop links context to state, making components re-render when values change. Without a Provider, you only get the static default value.

3. **Default Value Powers IntelliSense**  
   The structure defined in the default value enables IDE autocomplete and type hints, even though runtime values come from the Provider.

## useReducer Hook

**What**: An alternative to `useState` for managing complex state logic. Instead of updating state directly, you dispatch actions to a reducer function that determines how state changes.

**When to Use**: 
- State has complex update logic (multiple related values)
- Next state depends on previous state
- State transitions follow specific patterns/rules
- Multiple actions can update the same state

### Basic Structure

```jsx
const [state, dispatch] = useReducer(reducerFunction, initialState);
```

- **state**: Current state value
- **dispatch**: Function to trigger state updates by sending actions
- **reducerFunction**: Pure function that calculates new state
- **initialState**: Starting state value

### Reducer Function

A pure function that takes the current state and an action, then returns the new state:

```jsx
function reducerFunction(state, action) {
  // Determine what to do based on action.type
  if (action.type === 'INCREMENT') {
    return { count: state.count + 1 };
  }
  if (action.type === 'DECREMENT') {
    return { count: state.count - 1 };
  }
  return state; // Return unchanged state for unknown actions
}
```

**Naming Convention**: Use SCREAMING_SNAKE_CASE for action types to distinguish them as constants.

### Actions

Objects that describe what happened. Typically have a `type` property and optional payload data:

```jsx
// Simple action (just type)
dispatch({ type: 'INCREMENT' });

// Action with payload
dispatch({ 
  type: 'ADD_ITEM', 
  payload: { id: 1, name: 'Product' } 
});
```

### Complete Example

```jsx
import { useReducer } from 'react';

// Initial state
const initialState = { count: 0 };

// Reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    case 'DECREMENT':
      return { count: state.count - 1 };
    case 'RESET':
      return initialState;
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, initialState);
  
  return (
    <>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
      <button onClick={() => dispatch({ type: 'RESET' })}>Reset</button>
    </>
  );
}
```

### Key Rules

1. **Reducer Must Be Pure**: Same inputs always produce the same output, no side effects
2. **Never Mutate State**: Always return a new state object
3. **Return State for Unknown Actions**: Prevents crashes from typos
4. **State is Immutable**: Use spread operators or array methods that return new references

### useState vs useReducer

| useState | useReducer |
|----------|-----------|
| Simple state (strings, numbers, booleans) | Complex state (objects, arrays) |
| Independent state updates | Related state updates |
| Direct state manipulation | Centralized update logic |
| `setState(newValue)` | `dispatch({ type: 'ACTION' })` |

### Common Pattern: State + Payload

```jsx
function shoppingCartReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM':
      return {
        items: [...state.items, action.payload]
      };
    case 'REMOVE_ITEM':
      return {
        items: state.items.filter(item => item.id !== action.payload.id)
      };
    case 'UPDATE_QUANTITY':
      return {
        items: state.items.map(item =>
          item.id === action.payload.id
            ? { ...item, quantity: action.payload.quantity }
            : item
        )
      };
    default:
      return state;
  }
}
```

**Benefits**:
- All state logic is centralized in one function
- Easy to test reducer independently
- Action types make code self-documenting
- Easier to debug state transitions
