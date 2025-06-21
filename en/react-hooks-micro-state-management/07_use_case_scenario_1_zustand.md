# 07. Use Case Scenario 1: Zustand

> Re-rendering optimization with zustand, handling structured data

<br/>

## 🔖 1. What is Zustand?

### Key Features

- A lightweight state management library that allows simple and intuitive global state management in React applications
- You can define and use global state with just one or two hooks and simple function calls without complex setup
- When creating a store, you simply define state and actions that change state in object form
- It subscribes only to necessary state and re-renders only when that state changes, reducing unnecessary rendering
- Asynchronous logic (API communication, data fetching, etc.) can be easily included in state definition logic, making it convenient to handle side effects

### Example Code

> Adapted from [code on Zustand official documentation main page](https://zustand-demo.pmnd.rs/)

```jsx
import create from 'zustand';

// 1. Define global state (store)
// Create a store (useStore) by defining state and actions (increase, decrease, reset) with create function
const useStore = create((set) => ({
  count: 0,
  increase: () => set((state) => ({ count: state.count + 1 })),
  decrease: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}));

// When count value is updated through set, the state change function
// only components that depend on count re-render

function Counter() {
  // 2. Use state
  // When useStore hook is called within a component, you get count and each function
  const { count, increase, decrease, reset } = useStore();

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={increase}>A bear is born!!</button>
      <button onClick={decrease}>A bear dies..</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

export default function App() {
  return (
    <div>
      <h2>Zustand Bear 🐻</h2>
      <Counter />
    </div>
  );
}
```

### Should I Use Zustand?

- As such, Zustand defines a store and gets necessary state and functions through hooks
- Since global state is defined and managed at once, state change logic can be gathered in one place, so maintenance is expected to be easy even as scale grows
- Therefore, if you want to clearly structure global state at once and simplify state management flow through one store, Zustand would be good
- Jotai requires breaking down state into atom units from the beginning, so I'm not sure if it can be used well in the early stages of a project

<br/>

## 🔖 2. Immutable State Model

### Explaining 'Immutable State Model'

- When managing state, Zustand updates state by creating a new object with only the changed parts instead of modifying the original state object
- In other words, when state changes, instead of modifying the existing object, it creates a new object and puts that object in place of the existing state
- And unchanged parts (e.g., other fields or internal objects) reuse the previous ones as they are, reducing unnecessary rendering
- To use an analogy, instead of erasing content written on paper with an eraser and writing again, you write only the changed content on new paper and replace it. Unchanged content can reuse previous content without wasting resources

### Example Code

```jsx
import create from 'zustand';

const useStore = create((set) => ({
  user: { name: 'Ella', age: 20 },
  // State change function (maintaining immutability)
  updateUserName: (newName) =>
    set((state) => ({
      // Don't modify user object directly, return new object
      // Only name changes, age doesn't change, so reuse existing user.age
      user: { ...state.user, name: newName },
    })),

  items: ['apple', 'banana'],
  addItem: (item) =>
    set((state) => ({
      // Don't modify existing array, create new array and return
      items: [...state.items, item],
    })),
}));

function App() {
  const { user, updateUserName, items, addItem } = useStore();

  return (
    <div>
      <p>
        User: {user.name}, {user.age}
      </p>
      <button onClick={() => updateUserName('Ella')}>
        Change name to Chloe
      </button>

      <p>Items: {items.join(', ')}</p>
      <button onClick={() => addItem('orange')}>Give me Orange too</button>
    </div>
  );
}

export default App;
```

### Feeling the Benefits Through Examples

- As such, Zustand makes it easy to understand how state has changed
- By utilizing the immutability pattern that returns new objects, the process of comparing previous state and new state becomes simple
- When the book says "Since you only need to check the equality of references to state objects to know if there's a change, you don't need to check the entire value of objects," it means you can determine whether state has changed just by checking "Is it the same object or a different object?"

<br/>

## 🔖 3. Manual Rendering Optimization

### What is Selector-Based Re-rendering Control?

- It's a strategy to subscribe only to specific parts of state that components need and re-render components only when those parts actually change
- This way, even if other parts of global state change, components are not affected, reducing unnecessary re-rendering
- Components that use only a very small part of large global state will only render when that small part changes, which will be advantageous for performance optimization

### Example Code

```jsx
import create from 'zustand';

const useStore = create((set) => ({
  user: { name: 'Ella', age: 20 },
  items: ['apple', 'banana'],
  setName: (name) =>
    set((state) => ({
      user: { ...state.user, name },
    })),
  addItem: (item) =>
    set((state) => ({
      items: [...state.items, item],
    })),
}));

// Selector: get only user's name
const selectUserName = (state) => state.user.name;

// Selector: get only the length of items array
const selectItemsCount = (state) => state.items.length;

function UserNameDisplay() {
  // Subscribe only to user.name value, don't re-render if name doesn't change
  const name = useStore(selectUserName);
  console.log('UserNameDisplay rendered');
  return <div>User Name: {name}</div>;
}

function ItemsCountDisplay() {
  // Subscribe only to items.length value, don't re-render if items count doesn't change
  const count = useStore(selectItemsCount);
  console.log('ItemsCountDisplay rendered');
  return <div>Items Count: {count}</div>;
}

function App() {
  const setName = useStore((state) => state.setName);
  const addItem = useStore((state) => state.addItem);

  return (
    <div>
      <UserNameDisplay />
      <ItemsCountDisplay />
      <button onClick={() => setName('Bob')}>Change name to Chloe</button>
      <button onClick={() => addItem('orange')}>Give me Orange too</button>
    </div>
  );
}

export default App;
```

### My Thoughts

This chapter provides a practical introduction to Zustand, showing how it simplifies global state management compared to more complex solutions like Redux. The key insight is that Zustand provides a good balance between simplicity and functionality.

The discussion about immutable state models is particularly important - it shows how Zustand leverages React's optimization patterns to provide better performance without requiring complex setup.

The selector-based optimization examples demonstrate how Zustand can be used efficiently in real applications, showing that you can get good performance without over-engineering.

### Code Examples

```typescript
// ✅ GOOD: Basic Zustand store setup
interface BearState {
  bears: number;
  fish: number;
  increasePopulation: () => void;
  removeAllBears: () => void;
  addFish: (amount: number) => void;
}

const useBearStore = create<BearState>((set) => ({
  bears: 0,
  fish: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
  addFish: (amount) => set((state) => ({ fish: state.fish + amount })),
}));

// ✅ GOOD: Using selectors for performance optimization
function BearCounter() {
  const bears = useBearStore((state) => state.bears); // Only re-renders when bears change
  const increasePopulation = useBearStore((state) => state.increasePopulation);

  return (
    <div>
      <h1>{bears} bears around here...</h1>
      <button onClick={increasePopulation}>Add a bear</button>
    </div>
  );
}

function FishCounter() {
  const fish = useBearStore((state) => state.fish); // Only re-renders when fish change
  const addFish = useBearStore((state) => state.addFish);

  return (
    <div>
      <h1>{fish} fish in the sea</h1>
      <button onClick={() => addFish(5)}>Add 5 fish</button>
    </div>
  );
}

// ✅ GOOD: Complex state with nested objects
interface UserState {
  user: {
    name: string;
    age: number;
    preferences: {
      theme: 'light' | 'dark';
      language: string;
    };
  };
  updateName: (name: string) => void;
  updateAge: (age: number) => void;
  updateTheme: (theme: 'light' | 'dark') => void;
  updateLanguage: (language: string) => void;
}

const useUserStore = create<UserState>((set) => ({
  user: {
    name: 'Ella',
    age: 25,
    preferences: {
      theme: 'light',
      language: 'en',
    },
  },
  updateName: (name) =>
    set((state) => ({
      user: { ...state.user, name },
    })),
  updateAge: (age) =>
    set((state) => ({
      user: { ...state.user, age },
    })),
  updateTheme: (theme) =>
    set((state) => ({
      user: {
        ...state.user,
        preferences: { ...state.user.preferences, theme },
      },
    })),
  updateLanguage: (language) =>
    set((state) => ({
      user: {
        ...state.user,
        preferences: { ...state.user.preferences, language },
      },
    })),
}));

// ✅ GOOD: Selective subscriptions for nested state
function UserName() {
  const name = useUserStore((state) => state.user.name); // Only re-renders when name changes
  const updateName = useUserStore((state) => state.updateName);

  return (
    <div>
      <p>Name: {name}</p>
      <input
        value={name}
        onChange={(e) => updateName(e.target.value)}
        placeholder='Enter name'
      />
    </div>
  );
}

function ThemeToggle() {
  const theme = useUserStore((state) => state.user.preferences.theme); // Only re-renders when theme changes
  const updateTheme = useUserStore((state) => state.updateTheme);

  return (
    <button onClick={() => updateTheme(theme === 'light' ? 'dark' : 'light')}>
      Current theme: {theme}
    </button>
  );
}

// ✅ GOOD: Async actions with Zustand
interface TodoState {
  todos: Todo[];
  loading: boolean;
  error: string | null;
  fetchTodos: () => Promise<void>;
  addTodo: (text: string) => void;
  toggleTodo: (id: string) => void;
}

const useTodoStore = create<TodoState>((set, get) => ({
  todos: [],
  loading: false,
  error: null,
  fetchTodos: async () => {
    set({ loading: true, error: null });
    try {
      const response = await fetch('/api/todos');
      const todos = await response.json();
      set({ todos, loading: false });
    } catch (error) {
      set({ error: error.message, loading: false });
    }
  },
  addTodo: (text) =>
    set((state) => ({
      todos: [
        ...state.todos,
        { id: Date.now().toString(), text, completed: false },
      ],
    })),
  toggleTodo: (id) =>
    set((state) => ({
      todos: state.todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      ),
    })),
}));

// ✅ GOOD: Components using the async store
function TodoList() {
  const { todos, loading, error, fetchTodos } = useTodoStore();

  useEffect(() => {
    fetchTodos();
  }, [fetchTodos]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {todos.map((todo) => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}

function TodoItem({ todo }: { todo: Todo }) {
  const toggleTodo = useTodoStore((state) => state.toggleTodo);

  return (
    <li>
      <input
        type='checkbox'
        checked={todo.completed}
        onChange={() => toggleTodo(todo.id)}
      />
      <span
        style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
      >
        {todo.text}
      </span>
    </li>
  );
}
```
