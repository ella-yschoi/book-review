# 04. Sharing Module State with Subscriptions

> How to use module state for handling global state, using selectors and useSubscription

<br/>

## 🔖 1. Module State

### Book Explanation in Detail

- Module state refers to variables defined outside of functions. In other words, variables declared at the top level of a file.
- These variables can be global variables or variables that can only be accessed within that file.
- How should we use module state in React components?
- React components have their own state and lifecycle. (That's why managing this is important and that's why we're studying state management now..)
- However, to use module state that exists outside components, we need to detect changes in that state and update the component.
- For this, we use subscriptions. Through subscriptions, whenever module state changes, the component can detect that change and re-render the screen.

<br/>

## 🔖 2. How to Use Module State for Handling Global State in React

### Hooks that Cause Re-rendering in React

```jsx
let count = 0;

const Component1 = () => {
  const [state, setState] = useStore(count);
  const inc = () => {
    count += 1;
    setState(count);
  };

  return (
    <div>
      {state} <button onClick={inc}>+1</button>
    </div>
  );
};
```

- useState and useReducer: These two react to module state.
- In other words, when a button is clicked, useState's count reacts (increases or decreases), and the component re-renders.

<br/>

## 🔖 3. Using Selectors and useSubscription

### Purpose of Using Selectors

- Using selectors allows components to select and use only the parts of state they need.
- This prevents unnecessary re-rendering and optimizes performance.

### Applying to Project

```jsx
import React from 'react';
import useSubscription from './useSubscription';

const FileList = () => {
  const files = useSubscription((state) => state.files); // selector

  return (
    <ul>
      {files.map((file) => (
        <li key={file.id}>{file.name}</li>
      ))}
    </ul>
  );
};

export default FileList;
```

For example, as shown above, components use the `useSubscription` hook and Selector to subscribe only to the state they need.

### My Thoughts

This chapter introduces the concept of module state and subscriptions, which is a different approach to state management compared to React's built-in state. The key insight is that we can manage state outside of React components and use subscriptions to keep components in sync with that external state.

The use of selectors is particularly important for performance optimization - it allows components to only re-render when the specific parts of state they care about change, rather than when any part of the global state changes.

This pattern is commonly used in state management libraries like Redux and Zustand, where the state exists outside of React but components can subscribe to changes.

### Code Examples

```typescript
// ✅ GOOD: Module state with subscription pattern
interface AppState {
  user: User | null;
  files: File[];
  currentDirectory: string;
  loading: boolean;
}

// Module state (outside React components)
let appState: AppState = {
  user: null,
  files: [],
  currentDirectory: '/',
  loading: false,
};

// ✅ GOOD: Subscription system
type Subscriber = (state: AppState) => void;
const subscribers = new Set<Subscriber>();

function subscribe(subscriber: Subscriber) {
  subscribers.add(subscriber);
  return () => subscribers.delete(subscriber);
}

function notifySubscribers() {
  subscribers.forEach((subscriber) => subscriber(appState));
}

function updateState(updater: (state: AppState) => AppState) {
  appState = updater(appState);
  notifySubscribers();
}

// ✅ GOOD: Custom hook for subscribing to module state
function useSubscription<T>(selector: (state: AppState) => T): T {
  const [selectedState, setSelectedState] = useState(() => selector(appState));

  useEffect(() => {
    const unsubscribe = subscribe((state) => {
      const newSelectedState = selector(state);
      if (newSelectedState !== selectedState) {
        setSelectedState(newSelectedState);
      }
    });

    return unsubscribe;
  }, [selector, selectedState]);

  return selectedState;
}

// ✅ GOOD: Components using selectors for specific state
function UserProfile() {
  const user = useSubscription((state) => state.user); // Only re-renders when user changes

  if (!user) return <div>Please log in</div>;

  return (
    <div>
      <h2>Welcome, {user.name}</h2>
      <p>Email: {user.email}</p>
    </div>
  );
}

function FileExplorer() {
  const files = useSubscription((state) => state.files); // Only re-renders when files change
  const currentDirectory = useSubscription((state) => state.currentDirectory); // Only re-renders when directory changes

  return (
    <div>
      <h3>Files in {currentDirectory}</h3>
      <ul>
        {files.map((file) => (
          <li key={file.id}>{file.name}</li>
        ))}
      </ul>
    </div>
  );
}

function LoadingIndicator() {
  const loading = useSubscription((state) => state.loading); // Only re-renders when loading changes

  return loading ? <div>Loading...</div> : null;
}

// ✅ GOOD: Actions for updating module state
function setUser(user: User) {
  updateState((state) => ({ ...state, user }));
}

function setFiles(files: File[]) {
  updateState((state) => ({ ...state, files }));
}

function setCurrentDirectory(directory: string) {
  updateState((state) => ({ ...state, currentDirectory: directory }));
}

function setLoading(loading: boolean) {
  updateState((state) => ({ ...state, loading }));
}

// ✅ GOOD: Complex selectors for derived state
function useFileCount() {
  return useSubscription((state) => state.files.length);
}

function useUserFiles() {
  return useSubscription((state) =>
    state.files.filter((file) => file.ownerId === state.user?.id)
  );
}

function useCurrentDirectoryFiles() {
  return useSubscription((state) =>
    state.files.filter((file) => file.directory === state.currentDirectory)
  );
}
```
