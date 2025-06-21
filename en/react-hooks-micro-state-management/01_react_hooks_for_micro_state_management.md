# 01. React Hooks for Micro State Management

> Concepts of micro state management that gained attention with React hooks, technical aspects of useState and useReducer hooks

<br/>

## 🔖 1. What is Micro State Management

### Origin of State Management (?)

I like studying development like history, and it was good that the beginning of Chapter 1 briefly explained how state management has been done.

To summarize, React is a library for component-based development, and React hooks serve as the foundation for making components reusable. Furthermore, 'micro state management' is purpose-oriented while making state management more lightweight from the existing centralized approach.

The term 'purpose-oriented' is comprehensive, but thinking simply, as in the book's example, you can see that extracting a hook like `useCount` from UI components means logic with a count-related purpose.

### Different solutions for specific purposes with React hooks (examples)

1. **Form state should be handled separately from global state and cannot be solved with a single state** → Form state is 'temporary' data that changes according to user input, and often doesn't need to be shared with other components. Managing it as global state can increase unnecessary state updates and complexity. Therefore, it's efficient to manage it as local state within each form component.
2. **Server cache state has unique characteristics different from other states** → State that fetches and caches data from the server is important for data consistency, freshness, refetching, etc. It's difficult to meet these requirements with general state management. So we use libraries like React Query to manage server state.
3. **Navigation state is not suitable for single state because the original state exists in the browser** → Navigation state is closely connected to URL, browser history, etc. If this state is managed with general React state, it can conflict with the browser's default behavior. Therefore, we use libraries like React Router to synchronize browser navigation state with application state.

Based on the above cases, data with properties like temporary, freshness guarantee, potential conflict with browser default behavior, etc., should be managed locally or consider library application rather than globally.

### Why do applications with rich graphics need a lot of global state?

Graphic elements are usually updated in real-time, and user input, animations, rendering data, etc. are closely related to each other. For example, user input occurring in one component can have an immediate impact on another component's animation or graphic representation.

Our service logic will also have more graphic elements than now, so we'll need to pay more attention to global state management then. Otherwise, data synchronization problems might occur..

<br/>

## 🔖 2. How to Do Micro State Management with React Hooks

### Using React Hooks

Looking at the example code below, since `useCount` was separated from Component, we can add functionality without touching the component.

```jsx
const useCount = () => {
  const [count, setCount] = useState(0);
  return [count, setCount];
};

const Component = () => {
  const [count, setCount] = useCount();
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>+1</button>
    </div>
  );
};
```

I should look for things that can be made into custom hooks in the company code I'm refactoring these days. However, I need to be careful to consider reusability and not over-abstract.

### Custom hook? Util?

```jsx
export default function StorageContextProvider({ children }) {
  // ... define states and functions

  const handleUpload = async (files) => {
    // implement upload logic
  };

  const handleDrop = async (files) => {
    // implement drag and drop logic
  };

  return (
    <StorageContext.Provider
      value={
        {
          /* ... */
        }
      }
    >
      {children}
    </StorageContext.Provider>
  );
}
```

What if we separate `handleUpload` and `handleDrop` into separate hooks? If it's specific logic that's only used in this component anyway, reusability doesn't increase while code structure only becomes complex. It can be seen as easier to maintain by just keeping it inside the component.

Conversely, if the two logics are expected to be used elsewhere and manage state during the file upload process, it would be appropriate to separate them as custom hooks like above.

```jsx
import { useState, useEffect } from 'react';

const useFileDrop = (curDir, api) => {
  const [loading, setLoading] = useState(false);
  const [progress, setProgress] = useState({ filename: '', progress: 0 });
  const [fileNdirList, setFileNdirList] = useState([]);

  const handleDrop = async (files) => {
    setLoading(true);
    // implement logic...
    setLoading(false);
  };

  return {
    loading,
    progress,
    fileNdirList,
    handleDrop,
  };
};

export default useFileDrop;
```

Conclusion: The goal of the refactoring I'm currently doing is to cut all dependencies of context and manage components through modularization rather than state management. In this case, it seems better to just manage it as util. Phew~

### React hook functions should be 'pure'?

When I read the part in the book that says "React hook functions should be 'sufficiently' pure to work consistently even if called multiple times," I was confused at first.

Understanding it step by step, rather than the purity of pure functions, React hooks are designed for state management and side effect processing, so they're not perfect pure functions (they can't be in the first place), and the point is **ensuring predictable and consistent behavior**.

<br/>

## 🔖 3. Using useState

### Lazy Evaluation

```jsx
const heavyComputation = () => {
  // complex and time-consuming calculation
  let sum = 0;
  for (let i = 0; i < 1000000000; i++) {
    sum += i;
  }
  return sum;
};

const Component = () => {
  const [value, setValue] = useState(heavyComputation);
  // ...
};
```

The book's explanation of lazy evaluation was hard to understand, so let me explain it more clearly.

In the code above, `heavyComputation` is a time-consuming function. When you pass this function as an initialization function to `useState`, this function is called only when the component is first rendered to calculate the initial value of the state. After that, when the component is re-rendered, this function is not called again, so there's a performance benefit.

Simply put, it's calculated when needed. Comparing calling at first render vs calling at every render:

```jsx
// Pass as initialization function (called only once at first render)
const [state, setState] = useState(() => heavyComputation());

// Pass function result as initial state (called at every render)
const [state, setState] = useState(heavyComputation());
```

<br/>

## 🔖 4. Using useReducer

### Actions don't need to be objects?

```jsx
const reducer = (count, delta) => {
  if (delta < 0) {
    throw new Error('delta cannot be negative');
  }
  if (delta > 10) {
    // too big, just ignore
    return count;
  }
  if (count < 100) {
    // add bonus
    return count + delta + 10;
  }
  return count + delta;
};
```

In the `useReducer` hook, actions are generally expressed as objects, but they don't necessarily have to be objects. That is, actions can be any type of value. The `delta` in the example code is a value used as an action and is a number type (primitive value).

That is, in simple state update logic, actions can also be used as simple values like numbers or strings. For complex state management or cases where action types need to be distinguished, it's common to make actions objects and use a `type` field.

### Why 'action'?

Action contains information explaining how to change the state. The reducer function has the form `(state, action) => newState`, taking the current state and action and returning a new state. Literally, it's an action that returns state as newState and plays the role of instructing state changes.

<br/>

## 🔖 5. useState vs. useReducer

### Can they implement each other?

`useState` can be 100% implemented with `useReducer`.

```jsx
const useState = (initialState) => {
  const [state, dispatch] = useReducer(
    (prev, action) => (typeof action === 'function' ? action(prev) : action),
    initialState
  );
  return [state, dispatch];
};
```

Conversely, `useState` can 'almost' implement `useReducer`.

```jsx
const useReducer = (reducer, initialState) => {
  const [state, setState] = useState(initialState);
  const dispatch = (action) => setState((prev) => reducer(prev, action));
  return [state, dispatch];
};
```

> [!NOTE]
> When using `useState`: State update logic exists `inside` the component <br/>
> When using `useReducer`: State update logic exists `outside` the component <br/>

To add, the point of `useReducer` is **separating logic for updating state within one component from that component**. → Remember that it ultimately contributes to component optimization.

<br/>

## 📚 References

- [React Document - useReducer](https://react.dev/reference/react/useReducer)
- [React Hooks :: Learning about useReducer](https://velog.io/@iamhayoung/React-Hooks-useReducer%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)
