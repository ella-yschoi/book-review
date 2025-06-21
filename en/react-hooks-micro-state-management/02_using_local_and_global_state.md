# 02. Using Local and Global State

> About: Prop drilling pattern, what is global state?, when is it good to use global state?

<br/>

## 🔖 1. Problems in Our Project Code and Solution Direction

### Book Content

- If you create local state and only use that state in components and child components, it's good in terms of locality and reusability. Therefore, following this strategy is usually recommended.
- However, in certain situations, there are cases where common state is needed for two or more components that are far apart from each other in the tree. In such cases, global state is needed. Since global state doesn't belong to a specific component, we need to consider where to store the global state.

### Current: Managing with Context

- It's expected that common state will be needed for components like Header, Main, Panel that are far apart from each other in the tree, so states are managed globally
- e.g. Currently using the state representing the current directory path (`curDir`) globally, and `StorageContextProvider` exists within `StorageContext.tsx`

### Change: Managing with Props

- Global state management was overused, making state tracking difficult
- Since the state is relatively simple and updates don't occur frequently, it was judged as over-engineering
- Therefore, it was judged that managing with props would be simpler and more efficient
- So, we changed to a method where the parent component (`Storage.tsx`) creates local state and components and child components (Header, Main, Panel) receive and use arguments, i.e., props
- If necessary, consider using Context partially or mixing approaches
- e.g. `curDir` can only be used in each component that needs path management

### Connecting with Book Content

> [!NOTE]
>
> 1. What global states are used in our code? <br/>
> 2. And how much and where are those global states being used? <br/>
> 3. What components are experiencing unnecessary re-renders?

<br/>

## 🔖 2. Pure Functions vs. Callback Functions

### Book Content

- Functions that depend on global variables work identically as long as the variables don't change. Since the function's behavior can be changed from outside, it can be considered a powerful feature. However, the downside is that this function can be used elsewhere without knowing that it depends on external variables.
- When variables are singletons, creating a container object to wrap global variables is a more modularized approach to further increase code reusability.

### What I've Been Refactoring Lately

- Recently, while checking if our project code is well modularized or checking the overall modularization, I discovered the following problem and am currently refactoring.
- Callback functions were included inside pure functions, increasing the function's responsibility and causing side effects.
- For example, calling `getDirectory` as a callback inside the `renameDirectory` function made the `renameDirectory` function not only change directory names but also take on the role of getting changed directory information, violating the single responsibility principle.
- Additionally, it reduced function reusability and increased dependencies between functions, hindering code modularization and maintainability.

### Before: Pure Function Including Callback Function

```jsx
function renameDirectory(oldName, newName, callback) {
  // Directory name change logic
  callback(newName);
}

// Usage example
renameDirectory('oldDir', 'newDir', (updatedName) => {
  const directoryInfo = getDirectory(updatedName);
  // Additional logic
});
```

### After Refactoring: Combining Pure Functions

```jsx
function renameDirectory(oldName, newName) {
  // Directory name change logic
  return newName;
}

function getDirectory(directoryName) {
  // Logic to get directory information
  return directoryInfo;
}

// Function combination
const newDirName = renameDirectory('oldDir', 'newDir');
const directoryInfo = getDirectory(newDirName);
```

### Connecting with Book Content

> [!NOTE]
> When global variables are singletons, creating a container object to wrap global variables is a more modularized approach. This is similar to combining pure functions as shown in the example above to pass necessary data. <br/>
> Using container objects allows functions to access necessary data through the container, so they don't directly depend on global variables and dependencies become clear.

<br/>

## 🔖 3. The Meaning of 'Ensuring Locality'

### Connecting with Book Content

- The book says "Functions containing `useState` can be said to be 'suppressed' because variables can only be used within the function declaration scope," and the reason is that it's impossible to change variables from outside the function.
- Based on this point, when nothing outside the component has any influence, when the component's _independence_ is guaranteed, that is, when locality is guaranteed.

<br/>

## 🔖 4. When to Use Global State?

### When Passing Props is Not Appropriate

- Let's draw the component tree.
- At this time, if state needs to be shared between two components that are far apart from each other?
- What if we even need to lift state from depth 3 to the root?
- Since props must be passed through intermediate components, this is very inefficient.
- For example, if we need to pass the same prop through `Storage.tsx`, `StorageMain.tsx`, and `StorageSub.tsx` for `StorageHeader.tsx` (even though the listed components don't use that prop), how would someone seeing that code for the first time feel?

### When State Already Exists Outside React

- For example, there might be user authentication information obtained without React. In this case, global state can exist outside React.
- In addition, websockets received in real-time from servers, network status, clipboard data, etc. may also need to be managed globally.

<br/>

## 🔖 5. Key Points of Chapter 1

### Ask These Questions When Writing Code

> [!NOTE]
> Does each component exist independently? <br/>
> Are there unnecessary dependencies that don't affect performance?

### My Thoughts

This chapter provides a clear framework for deciding when to use local vs. global state. The key insight is that we should default to local state and only use global state when there's a compelling reason. The examples of prop drilling and external state sources are particularly helpful for making these decisions.

The refactoring example from callback functions to pure functions is excellent - it shows how we can improve code modularity by reducing dependencies and making functions more focused. This principle applies beyond just state management.

### Code Examples

```typescript
// ✅ GOOD: Local state management
function LocalStateExample() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// ✅ GOOD: Prop drilling for simple cases
function ParentComponent() {
  const [sharedData, setSharedData] = useState('initial');

  return (
    <div>
      <ChildComponent data={sharedData} onUpdate={setSharedData} />
    </div>
  );
}

// ✅ GOOD: Context for complex state sharing
const GlobalStateContext = createContext<{
  user: User | null;
  setUser: (user: User) => void;
}>({
  user: null,
  setUser: () => {},
});

function GlobalStateProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null);

  return (
    <GlobalStateContext.Provider value={{ user, setUser }}>
      {children}
    </GlobalStateContext.Provider>
  );
}

// ✅ GOOD: Pure functions without side effects
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

function formatCurrency(amount: number): string {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(amount);
}

// Usage: Combine pure functions
const total = calculateTotal(cartItems);
const formattedTotal = formatCurrency(total);
```
