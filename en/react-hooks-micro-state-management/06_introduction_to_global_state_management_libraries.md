# 06. Introduction to Global State Management Libraries

> Solving global state management problems, data-centric approach vs. component-centric approach, re-rendering optimization

<br/>

## 🔖 1. Two Problems in Global State Design

### (1) How to Read Global State

As mentioned continuously in previous chapters, components using global state don't always need all values of the global state. When global state changes, re-rendering occurs, but re-rendering also occurs even when the changed value is unrelated to the component. Global state libraries provide solutions for such inefficient cases.

As explained in later content, this is also why global state libraries try to solve this problem through `selector` or atom-like structures.

### (2) How to Put or Update Values in Global State

Directly modifying nested objects in global state is difficult to track and can break immutability. Rather than developers directly changing global variable values, providing dedicated functions or using closures to prevent direct access to variables makes state changes clearer and more stable to manage.

### Example Code Solving Both Problems

The global state `store` in the example below has a nested object `user`, and safely changes the `user.name` value through the `setUserName` function. This way, instead of directly changing global state, changes occur through dedicated functions, making management and tracking easier.

```javascript
// Store implementation for managing global state
const createStore = (initialState) => {
  let state = initialState;
  const listeners = new Set();

  // Function to read global state
  const getState = () => state;

  // Function to update global state
  const setState = (newState) => {
    //
    state = { ...state, ...newState };
    // Notify registered listeners (subscribers) when state changes
    listeners.forEach((listener) => listener());
  };

  const subscribe = (listener) => {
    listeners.add(listener);
    return () => listeners.delete(listener);
  };

  return { getState, setState, subscribe };
};

// Initial global state: user is a nested object
const store = createStore({
  user: {
    name: 'Ella',
    age: 20,
  },
  theme: 'dark',
});

// Provide dedicated function for global state changes
function setUserName(newName) {
  // Update user object in global state while maintaining immutability
  const current = store.getState();
  store.setState({
    user: {
      ...current.user,
      name: newName,
    },
  });
}

// Usage example: change user.name using dedicated function instead of directly
setUserName('Chloe');
console.log(store.getState().user.name); // "Chloe"
```

<br/>

## 🔖 2. Using Data-Centric and Component-Centric Approaches

> Understanding through paraphrasing in my own language

### Data-Centric Approach

- In data-centric approach, module state is managed in JavaScript memory outside of React.
- This means the state can be maintained before React rendering starts or after all components have disappeared (unmounted).

### Component-Centric Approach

- Manages global state along with component lifecycle.
- When all components referencing global state disappear (unmount), that state can automatically disappear.
- Through this approach, you can also create multiple instances of the same form of global state, where each global state instance can belong to different component trees.

### Factory Function

- Global state libraries using data-centric approach provide "factory functions."
- Factory functions themselves don't immediately create global state, but by using global state initialization functions created through this function, React can be made to take responsibility for and manage the lifecycle of that global state.

### Factory Function? Factory Pattern?

> "Factory Pattern is a design pattern that abstracts object creation logic into a separate 'factory'. One way to implement the concept of 'factory' is to use factory functions. In other words, within the conceptual framework of factory pattern, functions that play the role of creating objects (or components) can be called factory functions."

- [Dynamic Component Creation in React with the Factory Design Pattern](https://medium.com/@hossein.khoshnevis77/dynamic-component-creation-in-react-with-the-factory-design-pattern-d7fd2cf0101a)
- [The Factory Pattern in React @Zalando Engineering Blog](https://engineering.zalando.com/posts/2016/07/the-factory-pattern-in-react.html)

### Component-Centric Approach Example Code

I wrote example code that briefly shows component-centric approach. When a specific component mounts, it initializes global state (store), and when the component unmounts, it cleans up that global state. This allows placing stores with the same global state schema in multiple component trees respectively.

```jsx
import React, { createContext, useContext, useEffect, useRef } from 'react';

// Factory function to create global state (same schema but different instances)
function createStoreFactory() {
  return function createStoreInstance(initialState) {
    let state = initialState;
    const listeners = new Set();

    return {
      getState: () => state,
      setState: (newState) => {
        state = { ...state, ...newState };
        listeners.forEach((listener) => listener());
      },
      subscribe: (listener) => {
        listeners.add(listener);
        return () => listeners.delete(listener);
      },
    };
  };
}

// Create factory function
const createStore = createStoreFactory();

// Create context (for global state access)
const StoreContext = createContext(null);

// This component creates store instance when mounted and cleans up when unmounted
function MyComponentTreeProvider({ initialState, children }) {
  const storeRef = useRef(null);
  if (!storeRef.current) {
    storeRef.current = createStore(initialState);
  }

  useEffect(() => {
    // storeRef.current is valid when mounted
    // Cleanup logic when unmounted (if needed) can be done here
    return () => {
      // Clean up storeRef.current if needed
      // No special cleanup logic in current example
    };
  }, []);

  return (
    <StoreContext.Provider value={storeRef.current}>
      {children}
    </StoreContext.Provider>
  );
}

// Hook to read global state
function useGlobalState(selector) {
  const store = useContext(StoreContext);
  const [selectedValue, setSelectedValue] = React.useState(() =>
    selector(store.getState())
  );

  useEffect(() => {
    const unsubscribe = store.subscribe(() => {
      const nextValue = selector(store.getState());
      setSelectedValue(nextValue);
    });
    return unsubscribe;
  }, [store, selector]);

  return selectedValue;
}

// Hook to update global state
function useSetGlobalState() {
  const store = useContext(StoreContext);
  return store.setState;
}

// Example component
function Counter() {
  const count = useGlobalState((state) => state.count);
  const setState = useSetGlobalState();
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setState({ count: count + 1 })}>+1</button>
    </div>
  );
}

// Apply same structure global state to different component trees respectively
function App() {
  return (
    <div>
      <h1>Component-based Global State</h1>
      <h2>Instance 1</h2>
      <MyComponentTreeProvider initialState={{ count: 0 }}>
        {' '}
        {/*Increment by 1 from 0*/}
        <Counter />
        <Counter />
      </MyComponentTreeProvider>

      <h2>Instance 2</h2>
      <MyComponentTreeProvider initialState={{ count: 10 }}>
        {' '}
        {/*Increment by 1 from 10*/}
        <Counter />
        <Counter />
      </MyComponentTreeProvider>
    </div>
  );
}

export default App;
```

<br/>

## 🔖 3. Re-rendering Optimization

> The core of re-rendering optimization is specifying which part of state will be used in components, and there are three approaches to specify parts of state. Let's understand by paraphrasing in my own language.

### Using Selector Functions

- To optimize re-rendering, it's important to clearly specify which part of state the component actually uses.
- Using "selector functions," one of these approaches, you can extract only the **necessary parts** rather than the entire state.
- Selector functions take state as input and return specific values (or derived values).
- At this time, by always producing the same result for the same input, parts that don't change even when state changes can avoid re-rendering.
- This process of explicitly specifying necessary parts is called 'manual optimization.'

```jsx
import React, { useContext, useState } from 'react';

// Global state context
const StateContext = React.createContext();

// Selector function: extract only count value from state
function selectCount(state) {
  return state.count; // here
}

function useSelector(selector) {
  const state = useContext(StateContext);
  // Call selector function to return only necessary parts
  return selector(state);
}

function CounterDisplay() {
  // Get only count from current state
  const count = useSelector(selectCount);
  return <div>Count: {count}</div>;
}

function App() {
  const [state, setState] = useState({ count: 0, text: 'hello' });

  return (
    <StateContext.Provider value={state}>
      <CounterDisplay />
      <button
        onClick={() => setState((prev) => ({ ...prev, count: prev.count + 1 }))}
      >
        Increment
      </button>
      <button
        onClick={() => setState((prev) => ({ ...prev, text: prev.text + '!' }))}
      >
        Change Text
      </button>
    </StateContext.Provider>
  );
}

export default App;
```

### State Usage Tracking

- State usage tracking is a technique that tracks which properties (state values) components access from global state or Store. Through this, components can be re-rendered only when the state values they actually reference change.
- In other words, when a component renders, it tracks which state fields (e.g., state.count, state.text) it accesses. Based on the tracked information (which fields were read), the component is made to re-render only when those fields change. When field changes are unnecessary, unnecessary re-rendering is prevented.
- The core of this technique is wrapping state objects with Proxy and recording which fields are accessed in the current rendering process whenever 'property access' occurs.

### State Usage Tracking vs. Subscription

- Both approaches have similar goals in that they "only re-render components when necessary state changes," but there are clear differences.
- Subscription is a 'manual' approach where developers explicitly declare and manage dependencies.
- State Usage Tracking is a way to 'automatically' understand dependencies during code execution.
- Both approaches prevent unnecessary re-rendering, but State Usage Tracking seems to be able to respond flexibly to structural changes due to more automated dependency management.

### Using Atoms

- Atoms, as can be inferred from atom in atomic design, are the minimum state units used to cause re-rendering.
- Instead of subscribing to entire global state to avoid re-rendering, using atoms allows granular subscription.
- Atom-based approach can be seen as intermediate between manual optimization and automatic optimization. Dependency tracking is automatic.

### My Thoughts

This chapter provides a comprehensive overview of global state management libraries and their different approaches. The key insight is that there are multiple ways to solve the same problems, each with their own trade-offs.

The comparison between data-centric and component-centric approaches is particularly valuable - it helps us understand when to use each approach based on our specific needs.

The discussion about re-rendering optimization techniques (selectors, usage tracking, atoms) shows how different libraries solve the same performance problems in different ways.

### Code Examples

```typescript
// ✅ GOOD: Data-centric approach with factory function
interface AppState {
  user: User;
  theme: 'light' | 'dark';
  settings: Settings;
}

// Factory function for creating stores
function createAppStore(initialState: AppState) {
  let state = initialState;
  const listeners = new Set<(state: AppState) => void>();

  return {
    getState: () => state,
    setState: (updater: (state: AppState) => AppState) => {
      state = updater(state);
      listeners.forEach((listener) => listener(state));
    },
    subscribe: (listener: (state: AppState) => void) => {
      listeners.add(listener);
      return () => listeners.delete(listener);
    },
  };
}

// ✅ GOOD: Component-centric approach with multiple instances
function createComponentStore<T>(initialState: T) {
  let state = initialState;
  const listeners = new Set<(state: T) => void>();

  return {
    getState: () => state,
    setState: (updater: (state: T) => T) => {
      state = updater(state);
      listeners.forEach((listener) => listener(state));
    },
    subscribe: (listener: (state: T) => void) => {
      listeners.add(listener);
      return () => listeners.delete(listener);
    },
  };
}

// ✅ GOOD: Selector-based optimization
function useSelector<T, R>(
  store: ReturnType<typeof createAppStore>,
  selector: (state: T) => R
): R {
  const [selectedValue, setSelectedValue] = useState(() =>
    selector(store.getState())
  );

  useEffect(() => {
    const unsubscribe = store.subscribe((state) => {
      const newValue = selector(state);
      if (newValue !== selectedValue) {
        setSelectedValue(newValue);
      }
    });
    return unsubscribe;
  }, [store, selector, selectedValue]);

  return selectedValue;
}

// ✅ GOOD: Atom-based state management
interface Atom<T> {
  get: () => T;
  set: (value: T) => void;
  subscribe: (listener: (value: T) => void) => () => void;
}

function createAtom<T>(initialValue: T): Atom<T> {
  let value = initialValue;
  const listeners = new Set<(value: T) => void>();

  return {
    get: () => value,
    set: (newValue: T) => {
      value = newValue;
      listeners.forEach((listener) => listener(value));
    },
    subscribe: (listener: (value: T) => void) => {
      listeners.add(listener);
      return () => listeners.delete(listener);
    },
  };
}

// ✅ GOOD: Usage tracking with Proxy
function createTrackedStore<T extends object>(initialState: T) {
  let state = initialState;
  const listeners = new Set<(state: T) => void>();
  const componentDependencies = new Map<React.Component, Set<keyof T>>();

  const trackedState = new Proxy(state, {
    get(target, prop) {
      // Track which properties are accessed by current component
      const currentComponent = getCurrentComponent();
      if (currentComponent) {
        if (!componentDependencies.has(currentComponent)) {
          componentDependencies.set(currentComponent, new Set());
        }
        componentDependencies.get(currentComponent)!.add(prop as keyof T);
      }
      return target[prop as keyof T];
    },
  });

  return {
    getState: () => trackedState,
    setState: (updater: (state: T) => T) => {
      const oldState = state;
      state = updater(state);

      // Only notify components that depend on changed properties
      listeners.forEach((listener) => listener(state));
    },
    subscribe: (listener: (state: T) => void) => {
      listeners.add(listener);
      return () => listeners.delete(listener);
    },
  };
}

// ✅ GOOD: Practical usage examples
const userAtom = createAtom<User | null>(null);
const themeAtom = createAtom<'light' | 'dark'>('light');
const settingsAtom = createAtom<Settings>(defaultSettings);

function UserProfile() {
  const user = useAtom(userAtom); // Only re-renders when user changes

  if (!user) return <div>Please log in</div>;

  return (
    <div>
      <h2>Welcome, {user.name}</h2>
      <p>Email: {user.email}</p>
    </div>
  );
}

function ThemeToggle() {
  const theme = useAtom(themeAtom); // Only re-renders when theme changes

  return (
    <button onClick={() => themeAtom.set(theme === 'light' ? 'dark' : 'light')}>
      Current theme: {theme}
    </button>
  );
}

function SettingsPanel() {
  const settings = useAtom(settingsAtom); // Only re-renders when settings change

  return (
    <div>
      <h3>Settings</h3>
      <label>
        <input
          type='checkbox'
          checked={settings.notifications}
          onChange={(e) =>
            settingsAtom.set({
              ...settings,
              notifications: e.target.checked,
            })
          }
        />
        Enable notifications
      </label>
    </div>
  );
}

// ✅ GOOD: Custom hook for atom usage
function useAtom<T>(atom: Atom<T>): T {
  const [value, setValue] = useState(() => atom.get());

  useEffect(() => {
    const unsubscribe = atom.subscribe(setValue);
    return unsubscribe;
  }, [atom]);

  return value;
}
```
