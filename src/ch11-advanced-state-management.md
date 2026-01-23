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

**Two hooks available**:
- `useContext` — standard hook (all React versions)
- `use` — React 19+, more flexible (can be used inside if blocks)

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
