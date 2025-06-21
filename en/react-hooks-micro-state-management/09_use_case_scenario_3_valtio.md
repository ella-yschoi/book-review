# 09. Use Case Scenario 3: Valtio

> Change detection using proxy and immutable state generation, re-rendering optimization

<br/>

## 🔖 1. What is Valtio?

### Key Features

- A method that uses Proxy to track state and automatically updates only related components when state changes
- In other words, you can modify state objects as comfortably as handling them directly, and Valtio detects those changes and re-renders only necessary parts
- It wraps state objects with Proxy to detect all access to state. It identifies which parts of state each component reads and re-renders only that component when those parts change
- You can read current state snapshots with the `useSnapshot` hook, and automatically receive updated snapshots when state changes
- Since you can change state as you handle existing JS objects, the learning curve is low
- Being Proxy-based, caution may be needed in unpredictable scenarios

### Example Code

```jsx
import { proxy, useSnapshot } from 'valtio';

// 1. State definition: Use proxy function to wrap state with Proxy
const state = proxy({
  count: 0,
  text: 'Hello',
});

// 2. Component that reads and changes state
function Counter() {
  // Get current state snapshot with useSnapshot
  const snap = useSnapshot(state);
  return (
    <div>
      <p>Count: {snap.count}</p>
      <button onClick={() => state.count++}>+1</button>
    </div>
  );
}

function TextDisplay() {
  const snap = useSnapshot(state);
  return (
    <div>
      <p>Text: {snap.text}</p>
      <button onClick={() => (state.text = state.text + '!')}>Add "!"</button>
    </div>
  );
}

export default function App() {
  return (
    <div>
      <h1>Valtio Example</h1>
      <Counter />
      <TextDisplay />
    </div>
  );
}
```

<br/>

## 🔖 2. When is it Good to Use Valtio?

### When is it Good to Use Valtio?

- When you want to easily define and update global state
- When you want to change state intuitively like `state.count++`

### When is it Better Not to Use Valtio?

- When strict patterns are needed in large-scale projects (in such cases, it would be better to use consistent patterns like Redux's action, reducer)
- When complex debugging or tracking features are needed like Redux's MobX, Valtio's Proxy-based rendering optimization can become a disadvantage
- When type safety is needed, Valtio's loose structure can become a disadvantage

### My Thoughts

This chapter introduces Valtio, which takes a different approach to state management by using Proxy for automatic change detection. The key insight is that Valtio provides a more intuitive way to work with state by allowing direct mutations while still maintaining React's optimization benefits.

The discussion about when to use Valtio vs. when not to use it is particularly valuable - it shows that while Valtio is powerful and easy to use, it's not always the best choice for every project.

The Proxy-based approach is interesting because it provides automatic optimization without requiring developers to manually specify dependencies or use selectors.

### Code Examples

```typescript
// ✅ GOOD: Basic Valtio setup
import { proxy, useSnapshot } from 'valtio';

// Simple state object
const state = proxy({
  count: 0,
  name: 'Ella',
  theme: 'light' as 'light' | 'dark',
  todos: [] as Todo[],
});

// ✅ GOOD: Components using useSnapshot
function Counter() {
  const snap = useSnapshot(state); // Only re-renders when count changes

  return (
    <div>
      <h2>Count: {snap.count}</h2>
      <button onClick={() => state.count++}>Increment</button>
      <button onClick={() => state.count--}>Decrement</button>
    </div>
  );
}

function NameDisplay() {
  const snap = useSnapshot(state); // Only re-renders when name changes

  return (
    <div>
      <h2>Hello, {snap.name}!</h2>
      <input
        value={snap.name}
        onChange={(e) => (state.name = e.target.value)}
        placeholder='Enter name'
      />
    </div>
  );
}

function ThemeToggle() {
  const snap = useSnapshot(state); // Only re-renders when theme changes

  return (
    <button
      onClick={() => (state.theme = snap.theme === 'light' ? 'dark' : 'light')}
    >
      Current theme: {snap.theme}
    </button>
  );
}

// ✅ GOOD: Complex state with nested objects
interface User {
  id: string;
  name: string;
  email: string;
  preferences: {
    theme: 'light' | 'dark';
    language: string;
    notifications: boolean;
  };
}

const userState = proxy<User>({
  id: '1',
  name: 'Ella',
  email: 'ella@example.com',
  preferences: {
    theme: 'light',
    language: 'en',
    notifications: true,
  },
});

function UserProfile() {
  const snap = useSnapshot(userState);

  return (
    <div>
      <h2>{snap.name}</h2>
      <p>Email: {snap.email}</p>
      <p>Theme: {snap.preferences.theme}</p>
      <p>Language: {snap.preferences.language}</p>
    </div>
  );
}

function UserSettings() {
  const snap = useSnapshot(userState);

  const updateTheme = (theme: 'light' | 'dark') => {
    userState.preferences.theme = theme;
  };

  const updateLanguage = (language: string) => {
    userState.preferences.language = language;
  };

  const toggleNotifications = () => {
    userState.preferences.notifications = !snap.preferences.notifications;
  };

  return (
    <div>
      <h3>Settings</h3>
      <div>
        <label>
          Theme:
          <select
            value={snap.preferences.theme}
            onChange={(e) => updateTheme(e.target.value as 'light' | 'dark')}
          >
            <option value='light'>Light</option>
            <option value='dark'>Dark</option>
          </select>
        </label>
      </div>
      <div>
        <label>
          Language:
          <input
            value={snap.preferences.language}
            onChange={(e) => updateLanguage(e.target.value)}
          />
        </label>
      </div>
      <div>
        <label>
          <input
            type='checkbox'
            checked={snap.preferences.notifications}
            onChange={toggleNotifications}
          />
          Enable notifications
        </label>
      </div>
    </div>
  );
}

// ✅ GOOD: Array state management
interface Todo {
  id: string;
  text: string;
  completed: boolean;
}

const todoState = proxy({
  todos: [] as Todo[],
  filter: 'all' as 'all' | 'active' | 'completed',
});

function TodoList() {
  const snap = useSnapshot(todoState);

  const addTodo = (text: string) => {
    const newTodo: Todo = {
      id: Date.now().toString(),
      text,
      completed: false,
    };
    todoState.todos.push(newTodo);
  };

  const toggleTodo = (id: string) => {
    const todo = todoState.todos.find((t) => t.id === id);
    if (todo) {
      todo.completed = !todo.completed;
    }
  };

  const removeTodo = (id: string) => {
    const index = todoState.todos.findIndex((t) => t.id === id);
    if (index !== -1) {
      todoState.todos.splice(index, 1);
    }
  };

  const filteredTodos = snap.todos.filter((todo) => {
    if (snap.filter === 'active') return !todo.completed;
    if (snap.filter === 'completed') return todo.completed;
    return true;
  });

  return (
    <div>
      <div>
        <input
          placeholder='Add new todo'
          onKeyPress={(e) => {
            if (e.key === 'Enter' && e.currentTarget.value.trim()) {
              addTodo(e.currentTarget.value.trim());
              e.currentTarget.value = '';
            }
          }}
        />
      </div>

      <div>
        <button onClick={() => (todoState.filter = 'all')}>All</button>
        <button onClick={() => (todoState.filter = 'active')}>Active</button>
        <button onClick={() => (todoState.filter = 'completed')}>
          Completed
        </button>
      </div>

      <ul>
        {filteredTodos.map((todo) => (
          <TodoItem
            key={todo.id}
            todo={todo}
            onToggle={() => toggleTodo(todo.id)}
            onRemove={() => removeTodo(todo.id)}
          />
        ))}
      </ul>
    </div>
  );
}

function TodoItem({
  todo,
  onToggle,
  onRemove,
}: {
  todo: Todo;
  onToggle: () => void;
  onRemove: () => void;
}) {
  return (
    <li>
      <input type='checkbox' checked={todo.completed} onChange={onToggle} />
      <span
        style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
      >
        {todo.text}
      </span>
      <button onClick={onRemove}>Delete</button>
    </li>
  );
}

// ✅ GOOD: Async state management
const asyncState = proxy({
  data: null as any,
  loading: false,
  error: null as string | null,
});

async function fetchData() {
  asyncState.loading = true;
  asyncState.error = null;

  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    asyncState.data = data;
  } catch (error) {
    asyncState.error = error.message;
  } finally {
    asyncState.loading = false;
  }
}

function DataDisplay() {
  const snap = useSnapshot(asyncState);

  useEffect(() => {
    fetchData();
  }, []);

  if (snap.loading) return <div>Loading...</div>;
  if (snap.error) return <div>Error: {snap.error}</div>;
  if (!snap.data) return <div>No data</div>;

  return (
    <div>
      <h2>Data Loaded</h2>
      <pre>{JSON.stringify(snap.data, null, 2)}</pre>
    </div>
  );
}

// ✅ GOOD: Custom hooks for Valtio
function useValtioState<T>(proxyState: T) {
  const snap = useSnapshot(proxyState);
  return [snap, proxyState] as const;
}

function useValtioSelector<T, R>(proxyState: T, selector: (state: T) => R): R {
  const snap = useSnapshot(proxyState);
  return selector(snap);
}

// Usage example
function OptimizedCounter() {
  const count = useValtioSelector(state, (s) => s.count); // Only re-renders when count changes

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => state.count++}>Increment</button>
    </div>
  );
}
```
