# 03. Sharing Component State with React Context

> useState and useContext, context for global state, using context

<br/>

## 🔖 1. Using useContext with Static Values

### Context and Provider

- Using context allows you to pass values from parent components to child components in the tree below without using props. In other words, you can provide context to code even without props.
- When writing, you also need to clearly define the topic and what that topic does for context. Similarly, in context, the Provider (supplier) plays the role of providing context values to child components.
- For example, in the code below, the context says this: "Now, in this text (code), the protagonist (color) was black (default value), but in some situations it becomes red, and in other situations green...." At this time, the provider provides the values that color can become.

### Example Code

```jsx
import { createContext, useContext } from 'react';

const ColorContext = createContext('black');

const Component = () => {
  const color = useContext(ColorContext);
  return <div style={{ color }}>Hello {color}</div>;
};

const App = () => (
  <>
    <Component />
    <ColorContext.Provider value='red'>
      <Component />
    </ColorContext.Provider>
    <ColorContext.Provider value='green'>
      <Component />
    </ColorContext.Provider>
    <ColorContext.Provider value='blue'>
      <Component />
      <ColorContext.Provider value='skyblue'>
        <Component />
      </ColorContext.Provider>
    </ColorContext.Provider>
  </>
);

export default App;
```

<br/>

## 🔖 2. About Additional Re-rendering

### Over-engineering? or not?

- This wasn't covered importantly in the book, but it was something I was recently concerned about.
- Re-rendering is, as the book says, an unnecessary operation that should technically be avoided.
- However, I have had experience compromising to some extent when the performance impact wasn't significant compared to the effort required for refactoring. In other words, I didn't over-engineer unnecessarily.
- This was because I thought it was more important to do more important things within limited time, even though it could fill one line in a resume. The company would also understand this sufficiently (?)

<br/>

## 🔖 3. Creating Context for Global State

### Creating One State with useReducer and Propagating Through Multiple Contexts

- Manage one global state with `useReducer`. Then use multiple contexts to distribute parts of the state to components that need them.
- Distribute the state update function (`dispatch`) through a separate context so it can only be accessed when needed.
- The key is to use multiple contexts to avoid unnecessary re-rendering.

### Applying to Project

```jsx
import React, { createContext } from 'react';

// Create contexts for state fragments
const FilesContext = createContext();
const CurrentDirectoryContext = createContext();
const LoadingContext = createContext();

// Create context for state update function (dispatch)
const StorageDispatchContext = createContext();
```

- For example, there's a Storage application (e.g. Google Drive) that manages files and directories.
- Global state would include `files`: list of files in the current directory, `curDir`: current directory path, `loading`: file loading status, etc.
- And the state update function would include a dispatch function to handle file and directory related actions.

### My Thoughts

This chapter introduces the concept of using React Context for state sharing, which is a fundamental pattern in React applications. The key insight is that Context provides a way to share state without prop drilling, but it needs to be used carefully to avoid unnecessary re-renders.

The approach of splitting state into multiple contexts is particularly interesting - it shows how we can optimize performance by only re-rendering components that actually depend on the changed state. This is a more sophisticated approach than using a single global context.

The discussion about over-engineering vs. practical considerations is very relevant to real-world development. Sometimes the perfect solution isn't worth the effort, especially when the performance impact is minimal.

### Code Examples

```typescript
// ✅ GOOD: Multiple contexts for different state slices
interface StorageState {
  files: File[];
  currentDirectory: string;
  loading: boolean;
}

type StorageAction =
  | { type: 'SET_FILES'; payload: File[] }
  | { type: 'SET_CURRENT_DIRECTORY'; payload: string }
  | { type: 'SET_LOADING'; payload: boolean };

// Create separate contexts for different state slices
const FilesContext = createContext<File[]>([]);
const CurrentDirectoryContext = createContext<string>('');
const LoadingContext = createContext<boolean>(false);
const StorageDispatchContext = createContext<React.Dispatch<StorageAction>>(
  () => {}
);

// ✅ GOOD: Provider component that manages state
function StorageProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(storageReducer, initialState);

  return (
    <FilesContext.Provider value={state.files}>
      <CurrentDirectoryContext.Provider value={state.currentDirectory}>
        <LoadingContext.Provider value={state.loading}>
          <StorageDispatchContext.Provider value={dispatch}>
            {children}
          </StorageDispatchContext.Provider>
        </LoadingContext.Provider>
      </CurrentDirectoryContext.Provider>
    </FilesContext.Provider>
  );
}

// ✅ GOOD: Custom hooks for accessing specific state
function useFiles() {
  return useContext(FilesContext);
}

function useCurrentDirectory() {
  return useContext(CurrentDirectoryContext);
}

function useLoading() {
  return useContext(LoadingContext);
}

function useStorageDispatch() {
  return useContext(StorageDispatchContext);
}

// ✅ GOOD: Components only re-render when their specific state changes
function FileList() {
  const files = useFiles(); // Only re-renders when files change
  const dispatch = useStorageDispatch();

  return (
    <div>
      {files.map((file) => (
        <FileItem key={file.id} file={file} />
      ))}
    </div>
  );
}

function DirectoryBreadcrumb() {
  const currentDirectory = useCurrentDirectory(); // Only re-renders when directory changes

  return (
    <nav>
      <span>Current: {currentDirectory}</span>
    </nav>
  );
}

function LoadingIndicator() {
  const loading = useLoading(); // Only re-renders when loading state changes

  return loading ? <div>Loading...</div> : null;
}

// ✅ GOOD: Reducer for state management
function storageReducer(
  state: StorageState,
  action: StorageAction
): StorageState {
  switch (action.type) {
    case 'SET_FILES':
      return { ...state, files: action.payload };
    case 'SET_CURRENT_DIRECTORY':
      return { ...state, currentDirectory: action.payload };
    case 'SET_LOADING':
      return { ...state, loading: action.payload };
    default:
      return state;
  }
}
```
