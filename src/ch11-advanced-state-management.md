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

