# 08. Use Case Scenario 2: Jotai

> Rendering optimization with Jotai, various features like storing atom values

<br/>

## 🔖 1. What is Jotai?

### Key Features

- Jotai defines global state with 'atom' as the smallest state unit, and since only that atom is used in necessary components, state management feels natural like extending `useState` globally
- You can directly use atoms in components without using separate Providers. Therefore, you can manage global state without complex Context setup
- Components that only read specific atoms only react to those atoms. They don't render even when other atoms change, reducing unnecessary rendering
- Atoms can be connected to asynchronous data sources (e.g., fetch, async functions), making them suitable for loading states and asynchronous logic handling
- As a downside, there's a need to design where and how to divide state into atoms

### Example Code

```jsx
// Basic functions provided by Jotai: atom, useAtom
import { atom, useAtom } from 'jotai';

// 1. Define atom: atom to manage count as global state
const countAtom = atom(0);

// 2. Component that uses this atom
function Counter() {
  // Using useAtom returns [value, updateFunction],
  // so you can read and change count like useState
  // Instead of useState(0), use useAtom(countAtom)
  const [count, setCount] = useAtom(countAtom);

  return (
    <div>
      <h1>How many ghosts are left?: {count}</h1>
      <button onClick={() => setCount((c) => c + 1)}>Ghost +1 increase</button>
      <button onClick={() => setCount((c) => c - 1)}>Ghost -1 decrease</button>
    </div>
  );
}

export default function App() {
  return (
    <div>
      {/* Wherever Counter is rendered, state is shared through countAtom */}
      <Counter />
      <Counter />
    </div>
  );
}
```

<br/>

## 🔖 2. Top-down and Bottom-up Approaches

### Top-down Approach

- A method of first designing the global state structure needed for the entire application at the upper level, then finely dividing necessary parts from that structure and defining them as atoms
- Since the overall state model (e.g., global Store, etc.) is somewhat finalized in the early stages, it feels like drawing the big picture first and filling in detailed atoms later
- It would be useful when state is complex or when the overall state model structure is clear in the project
- However, much consideration is needed for state model design in the early stages, or you might have to do the work twice if the structure changes later

### Bottom-up Approach

- A method of creating necessary atoms one by one, gradually combining these atoms to ultimately form the global state structure
- Define the smallest unit, atoms, whenever needed, and gradually connect these atoms or create derived atoms to form complex state
- Easy to respond to requirement changes, but when state becomes very numerous later, the structure can become fragmented

### Is Jotai Bottom-up, Zustand Top-down?

- Looking at [some blog posts](https://team-beat.tistory.com/21), they say Jotai is Bottom-up and Zustand is Top-down. Personally, this somewhat applies to Zustand, but it doesn't seem entirely accurate for Jotai
- In Jotai's case, (as explained in the book) Jotai doesn't force a specific approach. In other words, Jotai is a library that can implement both Bottom-up and Top-down. As summarized above, it can be implemented differently depending on how you structure the state when designing
- On the other hand, Zustand gathers state and update logic in a store and components bring only necessary parts to use, so it can be seen as closer to Top-down
- The [official documentation](https://jotai.org/docs/basics/comparison#:~:text=Traditionally%2C%20a%20top,need%20for%20memoization.) says that Jotai, unlike traditional Top-down approaches (using selectors and memoization), inspired by Recoil, has natural Bottom-up approaches and advantages

<br/>

## 🔖 3. Adding Array Structure

### Atoms-in-Atom

- When managing array-form state in Jotai, put the array itself in one atom, and each element of that array references other atoms
- Simply put, it's a pattern of 'managing each element in an array (atom) as individual atoms'
- This way, when only a specific element of the array changes, **only the atom corresponding to that element is updated**, preventing unnecessary re-rendering
- Also, when adding or removing elements from the array, you only need to manipulate the atom corresponding to that element, making management easier

### Why Do This?

- Generally, if you put an entire array in one atom and directly change that array, the entire array is recognized as updated, so components using the entire array may re-render
- But in the Atoms-in-Atom approach, each element is separated into a separate atom, updating only the parts that need that element. In other words, even if one element of the array changes, components referencing other elements are not affected

### Example Code

```jsx
import { atom, useAtom } from 'jotai';

// 1. Think about creating an atom for each item
const createItemAtom = (initialValue) => atom(initialValue);

// 2. Put 'item atoms' inside the entire item list atom
const itemsAtom = atom([
  createItemAtom('Item 1'),
  createItemAtom('Item 2'),
  createItemAtom('Item 3'),
]);

function Item({ itemAtom }) {
  // Each itemAtom can be directly used with useAtom
  const [item, setItem] = useAtom(itemAtom);
  return (
    <div>
      <input value={item} onChange={(e) => setItem(e.target.value)} />
    </div>
  );
}

function ItemList() {
  const [itemAtoms, setItemAtoms] = useAtom(itemsAtom);

  const addItem = () => {
    // Add new item atom
    setItemAtoms([
      ...itemAtoms,
      createItemAtom(`Item ${itemAtoms.length + 1}`),
    ]);
  };

  return (
    <div>
      <button onClick={addItem}>Add item</button>
      <div>
        {itemAtoms.map((itemAtom, index) => (
          <Item key={index} itemAtom={itemAtom} />
        ))}
      </div>
    </div>
  );
}

// Item component directly receives itemAtom and only updates that item's value
// It's unrelated to other items, so even if this item's value changes, other items don't need to re-render

// Simply put, if only Item 1's atom value changes, only components subscribing to that atom are updated
// Components related to Item 2 or Item 3 remain unchanged and don't re-render

export default function App() {
  return (
    <div>
      <h1>Atoms-in-Atom Example Code</h1>
      <ItemList />
    </div>
  );
}
```

### My Thoughts

This chapter provides a comprehensive introduction to Jotai, showing how it differs from other state management solutions by using atoms as the fundamental unit. The key insight is that Jotai provides a more granular approach to state management.

The comparison between top-down and bottom-up approaches is particularly valuable - it shows how Jotai can be used flexibly depending on your project's needs and design philosophy.

The Atoms-in-Atom pattern is a powerful concept that demonstrates how Jotai can optimize rendering by allowing very fine-grained subscriptions to state changes.

### Code Examples

```typescript
// ✅ GOOD: Basic Jotai atom setup
import { atom, useAtom } from 'jotai';

// Simple atom for primitive values
const countAtom = atom(0);
const nameAtom = atom('Ella');
const themeAtom = atom<'light' | 'dark'>('light');

// ✅ GOOD: Derived atoms for computed values
const doubleCountAtom = atom((get) => get(countAtom) * 2);
const greetingAtom = atom((get) => `Hello, ${get(nameAtom)}!`);

// ✅ GOOD: Writable derived atoms
const incrementAtom = atom(
  (get) => get(countAtom),
  (get, set) => set(countAtom, get(countAtom) + 1)
);

// ✅ GOOD: Components using atoms
function Counter() {
  const [count, setCount] = useAtom(countAtom);
  const doubleCount = useAtom(doubleCountAtom)[0]; // Only get value

  return (
    <div>
      <p>Count: {count}</p>
      <p>Double: {doubleCount}</p>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}

function Greeting() {
  const [name, setName] = useAtom(nameAtom);
  const greeting = useAtom(greetingAtom)[0]; // Only get value

  return (
    <div>
      <p>{greeting}</p>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder='Enter name'
      />
    </div>
  );
}

// ✅ GOOD: Complex state with nested atoms
interface User {
  id: string;
  name: string;
  email: string;
  preferences: {
    theme: 'light' | 'dark';
    language: string;
  };
}

const userAtom = atom<User | null>(null);
const userPreferencesAtom = atom(
  (get) => get(userAtom)?.preferences ?? { theme: 'light', language: 'en' }
);
const userThemeAtom = atom(
  (get) => get(userPreferencesAtom).theme,
  (get, set, newTheme: 'light' | 'dark') => {
    const user = get(userAtom);
    if (user) {
      set(userAtom, {
        ...user,
        preferences: { ...user.preferences, theme: newTheme },
      });
    }
  }
);

// ✅ GOOD: Async atoms for data fetching
const fetchUserAtom = atom(async (get) => {
  const userId = get(userIdAtom);
  if (!userId) return null;

  const response = await fetch(`/api/users/${userId}`);
  return response.json();
});

const userLoadingAtom = atom((get) => {
  const user = get(fetchUserAtom);
  return user === undefined; // undefined means loading
});

// ✅ GOOD: Atoms-in-Atom pattern for arrays
interface Todo {
  id: string;
  text: string;
  completed: boolean;
}

// Create atom for individual todo
const createTodoAtom = (todo: Todo) => atom(todo);

// Atom containing array of todo atoms
const todosAtom = atom<Todo[]>([
  { id: '1', text: 'Learn Jotai', completed: false },
  { id: '2', text: 'Build app', completed: false },
]);

// Derived atom for todo atoms
const todoAtomsAtom = atom((get) =>
  get(todosAtom).map((todo) => createTodoAtom(todo))
);

function TodoItem({
  todoAtom,
}: {
  todoAtom: ReturnType<typeof createTodoAtom>;
}) {
  const [todo, setTodo] = useAtom(todoAtom);

  return (
    <li>
      <input
        type='checkbox'
        checked={todo.completed}
        onChange={(e) => setTodo({ ...todo, completed: e.target.checked })}
      />
      <span
        style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
      >
        {todo.text}
      </span>
    </li>
  );
}

function TodoList() {
  const [todoAtoms] = useAtom(todoAtomsAtom);

  return (
    <ul>
      {todoAtoms.map((todoAtom, index) => (
        <TodoItem key={index} todoAtom={todoAtom} />
      ))}
    </ul>
  );
}

// ✅ GOOD: Provider pattern for testing or isolation
import { Provider } from 'jotai';

function TestComponent() {
  return (
    <Provider>
      <Counter />
      <Greeting />
    </Provider>
  );
}

// ✅ GOOD: Custom hooks for atom combinations
function useUserTheme() {
  const [theme, setTheme] = useAtom(userThemeAtom);
  const [loading] = useAtom(userLoadingAtom);

  return {
    theme,
    setTheme,
    loading,
    isDark: theme === 'dark',
  };
}

function ThemeToggle() {
  const { theme, setTheme, loading } = useUserTheme();

  if (loading) return <div>Loading...</div>;

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Current theme: {theme}
    </button>
  );
}
```
