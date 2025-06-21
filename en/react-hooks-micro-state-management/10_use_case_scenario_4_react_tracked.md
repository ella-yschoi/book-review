# 10. Use Case Scenario 4: React Tracked

> Understanding React Tracked (using with useState, useReducer, React Redux)

<br/>

## 🔖 1. What is React Tracked?

### State Usage Tracking

- React Tracked is a state usage tracking library that automatically performs rendering optimization based on property detection
- It can be used with other state management libraries like Redux, Zustand, etc.
- It differs from simple global state management libraries in that it provides rendering optimization functionality

### Comparison with Context

- When only a specific value is changed, new context values are propagated, and `useContext` detects re-rendering. Therefore, unnecessary re-rendering can occur
- When using React Tracked, you define a `useTracked` hook that can be used instead of `useContext`. This wraps state with proxy and tracks usage

### Example Code

```jsx
const useFirstName = () => {
  const [{ firstName }] = useTracked();
  return firstName;
};
```

### Characteristics Seen Through Examples

- First, since the usage is the same as `useContext`, it has the advantage of being easy to apply
- The code is not much different from when using Context, but internally it tracks state usage and automatically optimizes rendering! A very clever fellow 👍
- The methods of using with `useState` and `useReducer` that come later also mainly deal with content similar to the above

<br/>

## 🔖 2. Pros and Cons of `useTracked`

### Characteristics and Advantages of `useTracked`

- As mentioned above, it tracks based on Proxy. `useTracked` wraps state objects with Proxy or uses similar techniques to intercept property (get) access. (Similar content also appeared in Chapter 9: Valtio) As a result, it can record which properties of that state object the component actually read (accessed)

- Only accessed properties trigger re-rendering. When rendering ends, `useTracked` recognizes "This component depends on properties A, B, C" and only tracks those properties. After that, if any of A, B, C changes, it re-renders the component using that hook. On the other hand, even if unused properties change, this component is not re-rendered. (Very efficient)

- It performs re-rendering optimization. Since only truly necessary fields from global state or large objects are considered "actual dependencies," unnecessary renders can be greatly reduced. For example, if only `user.name` was accessed, the component won't re-render even if `user.age` or `user.email` changes

### Disadvantages of `useTracked`

- There can be performance overhead in the process of recording which properties were read in each rendering process and detecting property access through Proxy. It might not be a big problem in general situations, but it would be good to be careful in cases with complex data structures or frequent rendering

- The more deeply nested the state structure is, the more tracking logic there will be. For example, in cases where you need to track properties that go deep like `obj.a.b.c`, unexpected performance costs or unexpected re-rendering might occur. (But this doesn't seem to be only a disadvantage of `useTracked`)

- Teams familiar with explicit action/reducer structures like Redux might feel that the automatic tracking provided by `useTracked` is abstract. Since state change flow is not clearly visible, state tracking/debugging might be difficult

<br/>

## 📚 References

- [dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)

### My Thoughts

This chapter introduces React Tracked, which provides automatic optimization by tracking which properties of state are actually used by components. The key insight is that React Tracked can be used as a drop-in replacement for `useContext` while providing much better performance.

The discussion about the pros and cons of `useTracked` is particularly valuable - it shows that while the automatic tracking is powerful, it comes with some trade-offs in terms of performance overhead and debugging complexity.

The comparison with Context is important because it shows how React Tracked solves a real problem that many developers face when using React Context for state management.

### Code Examples

```typescript
// ✅ GOOD: Basic React Tracked setup
import { createTrackedSelector } from 'react-tracked';

// Create tracked selector for state
const useTrackedState = createTrackedSelector<AppState>();

// ✅ GOOD: Custom hooks for specific state slices
function useUserName() {
  const [{ user }] = useTrackedState();
  return user.name; // Only re-renders when user.name changes
}

function useUserEmail() {
  const [{ user }] = useTrackedState();
  return user.email; // Only re-renders when user.email changes
}

function useUserPreferences() {
  const [{ user }] = useTrackedState();
  return user.preferences; // Only re-renders when user.preferences changes
}

// ✅ GOOD: Components using tracked state
function UserProfile() {
  const name = useUserName(); // Only re-renders when name changes
  const email = useUserEmail(); // Only re-renders when email changes

  return (
    <div>
      <h2>{name}</h2>
      <p>Email: {email}</p>
    </div>
  );
}

function ThemeToggle() {
  const preferences = useUserPreferences(); // Only re-renders when preferences change

  return (
    <button
      onClick={() =>
        updateTheme(preferences.theme === 'light' ? 'dark' : 'light')
      }
    >
      Current theme: {preferences.theme}
    </button>
  );
}

// ✅ GOOD: Using with useState
function createTrackedState<T>(initialState: T) {
  const [state, setState] = useState(initialState);
  const useTrackedState = createTrackedSelector<T>();

  return {
    state,
    setState,
    useTracked: useTrackedState,
  };
}

const { state, setState, useTracked } = createTrackedState({
  count: 0,
  name: 'Ella',
  theme: 'light' as 'light' | 'dark',
});

function Counter() {
  const [{ count }] = useTracked(); // Only re-renders when count changes

  return (
    <div>
      <p>Count: {count}</p>
      <button
        onClick={() => setState((prev) => ({ ...prev, count: prev.count + 1 }))}
      >
        Increment
      </button>
    </div>
  );
}

function NameDisplay() {
  const [{ name }] = useTracked(); // Only re-renders when name changes

  return (
    <div>
      <p>Name: {name}</p>
      <input
        value={name}
        onChange={(e) =>
          setState((prev) => ({ ...prev, name: e.target.value }))
        }
        placeholder='Enter name'
      />
    </div>
  );
}

// ✅ GOOD: Using with useReducer
type AppAction =
  | { type: 'INCREMENT' }
  | { type: 'DECREMENT' }
  | { type: 'SET_NAME'; payload: string }
  | { type: 'TOGGLE_THEME' };

function appReducer(state: AppState, action: AppAction): AppState {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'DECREMENT':
      return { ...state, count: state.count - 1 };
    case 'SET_NAME':
      return { ...state, name: action.payload };
    case 'TOGGLE_THEME':
      return { ...state, theme: state.theme === 'light' ? 'dark' : 'light' };
    default:
      return state;
  }
}

function createTrackedReducer<T, A>(
  reducer: (state: T, action: A) => T,
  initialState: T
) {
  const [state, dispatch] = useReducer(reducer, initialState);
  const useTrackedState = createTrackedSelector<T>();

  return {
    state,
    dispatch,
    useTracked: useTrackedState,
  };
}

const { state, dispatch, useTracked } = createTrackedReducer(appReducer, {
  count: 0,
  name: 'Ella',
  theme: 'light' as 'light' | 'dark',
});

function OptimizedCounter() {
  const [{ count }] = useTracked(); // Only re-renders when count changes

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>Increment</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>Decrement</button>
    </div>
  );
}

function OptimizedNameDisplay() {
  const [{ name }] = useTracked(); // Only re-renders when name changes

  return (
    <div>
      <p>Name: {name}</p>
      <input
        value={name}
        onChange={(e) =>
          dispatch({ type: 'SET_NAME', payload: e.target.value })
        }
        placeholder='Enter name'
      />
    </div>
  );
}

// ✅ GOOD: Using with Redux
import { useSelector } from 'react-redux';
import { createTrackedSelector } from 'react-tracked';

// Create tracked selector for Redux state
const useTrackedReduxState = createTrackedSelector<RootState>();

function ReduxCounter() {
  const [{ counter }] = useTrackedReduxState(); // Only re-renders when counter changes

  return (
    <div>
      <p>Count: {counter.value}</p>
      <button onClick={() => dispatch(increment())}>Increment</button>
    </div>
  );
}

function ReduxUserProfile() {
  const [{ user }] = useTrackedReduxState(); // Only re-renders when user changes

  return (
    <div>
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
    </div>
  );
}

// ✅ GOOD: Performance comparison
function PerformanceComparison() {
  // Without React Tracked - re-renders on any state change
  const state = useSelector((state: RootState) => state);

  // With React Tracked - only re-renders when accessed properties change
  const [{ user, counter }] = useTrackedReduxState();

  return (
    <div>
      <p>User: {user.name}</p>
      <p>Count: {counter.value}</p>
    </div>
  );
}

// ✅ GOOD: Custom tracked selectors for complex state
function useUserTheme() {
  const [{ user }] = useTrackedReduxState();
  return user.preferences.theme; // Only re-renders when theme changes
}

function useUserNotifications() {
  const [{ user }] = useTrackedReduxState();
  return user.preferences.notifications; // Only re-renders when notifications change
}

function ThemeToggle() {
  const theme = useUserTheme(); // Only re-renders when theme changes

  return (
    <button onClick={() => dispatch(toggleTheme())}>
      Current theme: {theme}
    </button>
  );
}

function NotificationToggle() {
  const notifications = useUserNotifications(); // Only re-renders when notifications change

  return (
    <label>
      <input
        type='checkbox'
        checked={notifications}
        onChange={(e) => dispatch(toggleNotifications(e.target.checked))}
      />
      Enable notifications
    </label>
  );
}
```
