# 05. Sharing Component State with Context and Subscriptions

> Limitations of module state, when context usage is needed, context and subscription patterns

<br/>

## 🔖 1. Comparing Context, Props, and Subscription

### Context

Components in the tree structure below can access necessary values without going through intermediate components with Props one by one. Also, if different providers are used for one Context, each provider can provide different values for each subtree it wraps.

### Props

In fact, with Props alone, you can give different values in the process of going from top to bottom tree. However, in this case, Props must be continuously passed (Props drilling) on every path going down, so management becomes difficult in deep tree structures and it's not good in terms of readability.

### Subscription

Using subscription patterns or `useSubscription` can reduce unnecessary re-rendering by making only components that need changed state re-render. For example, if you directly subscribe to global state management or context values and only re-render necessary parts, you can avoid situations where components unrelated to state changes are re-rendered.

### What to Apply to the Project?

Currently in the project, we were mainly using Context and then moving to Props. Then last week, as I refactored more and more, I realized that it's difficult to manage all state 100% with props. Therefore, I want to proceed in the following direction:

> [!NOTE]
>
> 1. If multiple components need the same state, manage that state with Context.
> 2. For states that don't need to be used globally, utilize `useState` or `useReducer` in each component. This clearly defines the scope of responsibility for each state and localizes it by managing each state independently.
> 3. If some components re-render too frequently due to global state changes, use `useSubscription` to ensure only necessary components are updated. This is adopted as a strategy to respond when performance issues actually occur.

<br/>

## 🔖 2. Limitations of Module State

### Understanding Module State Easily

> Module state has limitations in that it cannot have different states for each component tree or subtree because it is a globally defined singleton that exists outside React components.

- "Module state" is not something that can be created multiple times like components or contexts, but a variable (singleton) that is defined exactly once globally in the project and shared.
- For example, if you set one global variable, that value is accessed identically anywhere in the app. Therefore, you cannot have that variable differently for each component tree or subtree.
- Simply put, "module state" is like one TV shared by the whole family. You have to watch the same channel (state) in any room (component tree) in the house (app), and you can't set different channels for each room!
- Therefore, as mentioned in the book, module state has limitations in that it cannot have different states for each subtree.

<br/>

## 🔖 3. When Context Usage is Needed

### Cases of Using the Same Component Continuously

> In what situations can the situation of using the same component continuously be applied? I didn't have much experience with this, so I looked into it.

- **Repeating UI patterns**: It would also be useful when you want to show current logged-in user information in multiple areas like the top of the my page and sidebar.
- As mentioned in the book (p. 101), if three components have almost identical code except for different providers, this is exactly where React Context is suitable to use. It can be seen as a kind of signal.
- **List or card UI**: Let's say there are sorting options and filter information managed as global state. Let's say there's a card component that references this state, and this card component is displayed multiple times on the screen. It would be good to use when all refer to the same global state but render the same component continuously in the UI.

### Example Code for Using the Same Component Continuously

I wrote a simple example code that manages global state (sorting options, filter information) with context, and each card component subscribes to only necessary parts through `useContext` and `useSubscription` to render.

```jsx
import React, { createContext, useContext, useMemo, useState } from 'react';
import { useSubscription } from 'use-subscription';

// Function to create store for global state management
function createStore(initialState) {
  let state = initialState; // Variable to store state
  const listeners = new Set(); // Set to hold callbacks called when state changes

  return {
    // Function that returns current state
    getState: () => state,
    // Function to update state (merge objects then notify all listeners)
    setState: (newState) => {
      state = { ...state, ...newState };
      listeners.forEach((listener) => listener());
    },
    // Listener (callback) registration function, execute callback when state changes
    subscribe: (listener) => {
      listeners.add(listener);
      // Return function for unsubscribing
      return () => listeners.delete(listener);
    },
  };
}

// Initial state: set filter to 'ALL'
const store = createStore({ filter: 'ALL' });

// Create context to provide store
const StoreContext = createContext(store);

// useFilter hook to subscribe to and get filter state
function useFilter() {
  const store = useContext(StoreContext); // Get store from context
  const subscription = useMemo(
    () => ({
      // Function that returns current filter state
      getCurrentValue: () => store.getState().filter,
      // Function to register callback called when state changes
      subscribe: (callback) => store.subscribe(callback),
    }),
    [store] // Only recreate when store changes
  );
  return useSubscription(subscription); // React to filter state changes with useSubscription
}

// Hook that returns function to change filter state
function useSetFilter() {
  const store = useContext(StoreContext); // Get store from context
  // Return function to set new filter value
  return (newFilter) => store.setState({ filter: newFilter });
}

// Component that receives item information, determines if it matches filter conditions, and renders
function Card({ item }) {
  const currentFilter = useFilter(); // Get current filter with hook made above
  const visible = currentFilter === 'ALL' || item.type === currentFilter; // Check if current item type matches filter conditions

  // Show item if it matches filter conditions, otherwise return null
  return visible ? (
    <div style={{ border: '1px solid #333', margin: '4px', padding: '8px' }}>
      {item.name}
    </div>
  ) : null;
}

// Component with buttons to change filter
function FilterControls() {
  const setFilter = useSetFilter(); // Get filter change function
  return (
    <div>
      <button onClick={() => setFilter('ALL')}>ALL</button>{' '}
      {/* Button to change to ALL filter */}
      <button onClick={() => setFilter('BOOK')}>BOOK</button> {/* Button to change to BOOK filter */}
      <button onClick={() => setFilter('TOY')}>TOY</button>{' '}
      {/* Button to change to TOY filter */}
    </div>
  );
}

// Example item array (type: BOOK or TOY)
const items = [
  { name: 'Item 1', type: 'BOOK' },
  { name: 'Item 2', type: 'TOY' },
  { name: 'Item 3', type: 'BOOK' },
];

export default function App() {
  return (
    // Wrap with StoreContext.Provider to provide global state
    <StoreContext.Provider value={store}>
      <h1>Cards with global filter</h1>
      <FilterControls /> {/* Filter change component */}
      <div style={{ display: 'flex' }}>
        {/* Iterate through items array and render Card components */}
        {items.map((item, index) => (
          <Card key={index} item={item} />
        ))}
      </div>
    </StoreContext.Provider>
  );
}
```

<br/>

## 🔖 4. Diving Deeper into useSubscription

> [Example code where React uses the `useSubscription` hook](https://github.com/facebook/react/blob/7283a213dbbc31029e65005276f12202558558fc/packages/react-devtools-shared/src/devtools/views/Settings/SettingsModalContextToggle.js#L16)

In React code, the `useSubscription` hook was used to implement a subscription mechanism for re-rendering components by reacting to changes in external state.

### (1) Creating Subscription Object

```jsx
const isProfilingSubscription = useMemo(
  () => ({
    getCurrentValue: () => profilerStore.isProfilingBasedOnUserInput,
    subscribe: (callback: Function) => {
      profilerStore.addListener('isProfiling', callback);
      return () => profilerStore.removeListener('isProfiling', callback);
    },
  }),
  [profilerStore]
);
```

First, using `useMemo`, we create a subscription object called `isProfilingSubscription`. This object has two main properties.

- `getCurrentValue`: Function that returns the necessary value from current `profilerStore`
- `subscribe`: Function to register callback function called whenever value changes and return function for unregistration

Here, through `profilerStore.addListener('isProfiling', callback)`, we set up subscription to call `callback` whenever the `isProfiling` state of `profilerStore` changes, and in the `return` part, we also provide a function to unsubscribe from that subscription.

### (2) Connecting Subscription with useSubscription

When we pass this subscription object `isProfilingSubscription` to the `useSubscription` hook, the component gets "real-time reaction" to the `profilerStore.isProfilingBasedOnUserInput` value.

```jsx
const isProfiling = useSubscription < boolean > isProfilingSubscription;
```

`useSubscription` internally detects external state changes through the `subscribe` function, and when the value changes, it re-renders the component to keep the `isProfiling` value up to date.

### (3) Using the Latest Value

The `isProfiling` value obtained this way is reflected in the `disabled` property of the `Button` component below, and the button is activated/deactivated according to state changes in `profilerStore` (i.e., `isProfiling` state changes).

```jsx
<Button disabled={isProfiling} onClick={showFilterModal} title='View settings'>
  <ButtonIcon type='settings' />
</Button>
```

### Summary

We implemented subscription logic concisely to connect the `profilerStore` external state with components using `useSubscription`, and automatically update components whenever that state changes.

### My Thoughts

This chapter provides a comprehensive comparison of different state management approaches and their trade-offs. The key insight is that there's no one-size-fits-all solution - we need to choose the right tool based on the specific requirements of our application.

The discussion about module state limitations is particularly important - it helps us understand when we need more sophisticated state management patterns like Context or subscriptions.

The example with the card filtering system is excellent because it shows how Context and subscriptions can work together to create efficient, maintainable state management.

### Code Examples

```typescript
// ✅ GOOD: Hybrid approach combining Context and subscriptions
interface AppState {
  user: User | null;
  theme: 'light' | 'dark';
  filters: FilterState;
  notifications: Notification[];
}

// ✅ GOOD: Multiple contexts for different concerns
const UserContext = createContext<User | null>(null);
const ThemeContext = createContext<'light' | 'dark'>('light');
const FilterContext = createContext<FilterState>({});
const NotificationContext = createContext<Notification[]>([]);

// ✅ GOOD: Store with subscription pattern
class AppStore {
  private state: AppState;
  private listeners = new Set<(state: AppState) => void>();

  constructor(initialState: AppState) {
    this.state = initialState;
  }

  getState(): AppState {
    return this.state;
  }

  setState(updater: (state: AppState) => AppState): void {
    this.state = updater(this.state);
    this.notifyListeners();
  }

  subscribe(listener: (state: AppState) => void): () => void {
    this.listeners.add(listener);
    return () => this.listeners.delete(listener);
  }

  private notifyListeners(): void {
    this.listeners.forEach((listener) => listener(this.state));
  }
}

// ✅ GOOD: Provider component with multiple contexts
function AppProvider({ children }: { children: React.ReactNode }) {
  const [store] = useState(() => new AppStore(initialState));

  return (
    <UserContext.Provider value={store.getState().user}>
      <ThemeContext.Provider value={store.getState().theme}>
        <FilterContext.Provider value={store.getState().filters}>
          <NotificationContext.Provider value={store.getState().notifications}>
            {children}
          </NotificationContext.Provider>
        </FilterContext.Provider>
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

// ✅ GOOD: Custom hooks for specific state slices
function useUser() {
  return useContext(UserContext);
}

function useTheme() {
  return useContext(ThemeContext);
}

function useFilters() {
  return useContext(FilterContext);
}

function useNotifications() {
  return useContext(NotificationContext);
}

// ✅ GOOD: Subscription-based hook for complex state
function useAppState<T>(selector: (state: AppState) => T): T {
  const [selectedState, setSelectedState] = useState(() =>
    selector(store.getState())
  );

  useEffect(() => {
    const unsubscribe = store.subscribe((state) => {
      const newSelectedState = selector(state);
      if (newSelectedState !== selectedState) {
        setSelectedState(newSelectedState);
      }
    });

    return unsubscribe;
  }, [selector, selectedState]);

  return selectedState;
}

// ✅ GOOD: Components using appropriate state management
function UserProfile() {
  const user = useUser(); // Simple context for user data

  if (!user) return <div>Please log in</div>;

  return (
    <div>
      <h2>Welcome, {user.name}</h2>
      <p>Email: {user.email}</p>
    </div>
  );
}

function ThemeToggle() {
  const theme = useTheme(); // Simple context for theme

  return (
    <button
      onClick={() =>
        store.setState((state) => ({
          ...state,
          theme: state.theme === 'light' ? 'dark' : 'light',
        }))
      }
    >
      Current theme: {theme}
    </button>
  );
}

function FilteredItemList() {
  const filters = useFilters(); // Context for filters
  const items = useAppState((state) =>
    state.items.filter(
      (item) => filters.category === 'all' || item.category === filters.category
    )
  ); // Subscription for filtered items

  return (
    <div>
      {items.map((item) => (
        <ItemCard key={item.id} item={item} />
      ))}
    </div>
  );
}

function NotificationBadge() {
  const notifications = useNotifications(); // Context for notifications
  const unreadCount = useAppState(
    (state) => state.notifications.filter((n) => !n.read).length
  ); // Subscription for derived state

  return unreadCount > 0 ? <span className='badge'>{unreadCount}</span> : null;
}
```
