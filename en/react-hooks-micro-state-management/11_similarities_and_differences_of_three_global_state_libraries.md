# 11. Similarities and Differences of Three Global State Libraries

> Finally, let's compare Zustand, Jotai, and Valtio!

<br/>

## 🔖 1. Explaining the Three Libraries

### Zustand

- Similar to Redux (or React Redux) in terms of usage and Store model. However, it doesn't force action/reducer structure like Redux
- Unlike Redux, it's not based on reducer functions, so there's less boilerplate and lower learning curve
- In short, Zustand has higher freedom than Redux. It feels easier because it doesn't force much. (The book expresses it as 'doesn't present opinions on structure')
- Works by getting global state with `useStore` hook and defining simple `setters`
- Selector-based optimization: Can subscribe to only desired state with `useStore(selector, equalityFn)` form, reducing unnecessary re-rendering

### Jotai

- Manages state in atom units like Recoil, but doesn't use separate selector API. Instead, combines atoms or creates derived atoms to compose necessary state
- Only subscribes to necessary atoms, so components don't re-render unless atoms change. Can handle global state without Context setup
- Can manage by breaking down into multiple small atoms or combine atoms to create complex logic. So even as project scale grows, it can maintain relatively flexible structure
- So teams that used Recoil seem to switch to Jotai a lot after Recoil declined

### Valtio

- Similar to MobX in that it's a mutable update model, but the point is that Valtio automatically detects property changes through Proxy
- Only re-renders when properties that components actually read change. Can write intuitively like `state.count++` without immutability management required in Redux, etc.
- Advantages include being easy to learn and enabling intuitive code writing
- Disadvantages include that for developers familiar with explicit action/reducer approaches in large-scale projects, tracking logic might seem very abstract, making debugging difficult

<br/>

## 🔖 2. Comparing Three Libraries in a Table

> Thanks to GPT (❁´◡`❁)

| Library     | Core Concept/Usage                                                                                                                            | Main Features                                                                                                             | Advantages                                                                                                                                                                             | Disadvantages                                                                                                                                                                                     |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Zustand** | - Single (or few) Store objects<br/>- Use state/actions through `useStore` hook<br/>- Redux-like structure (but doesn't force reducer/action) | - Very simple setup, minimal boilerplate<br/>- Subscribe to only desired state (Selector+Equality)                        | - High freedom and low learning curve<br/>- Can subscribe to only necessary parts → unnecessary rendering ↓                                                                            | - Doesn't force structure, so design can be inconsistent as scale grows<br/>- DevTools/middleware ecosystem not as large as Redux                                                                 |
| **Jotai**   | - Define state in **atom** units<br/>- Read/write atoms with `useAtom` hook<br/>- Recoil-like (use derived atoms instead of Selectors)        | - Only reads necessary atoms, so no effect even if other atoms change<br/>- Can manage global state without Context setup | - Flexible in dividing small unit states into multiple atoms and combining them<br/>- Easy to learn and apply<br/>- Can compose complex logic through atom combination (derived atoms) | - Structure management becomes complex if atoms become too numerous<br/>- Official DevTools somewhat limited (not as mature as Recoil)                                                            |
| **Valtio**  | - **Proxy**-based<br/>- Use state snapshots with `useSnapshot` hook<br/>- Can modify directly like `state.someProp++`                         | - Mutable update approach<br/>- Only updates properties that components actually read (automatic tracking)                | - Very intuitive and free syntax<br/>- Immutability management, concise code<br/>- Automated partial rendering optimization                                                            | - Tracking costs may increase with indiscriminate state changes<br/>- Difficult debugging in large-scale projects because it's not action/reducer-based<br/>- Small DevTools/middleware ecosystem |

<br/>

## 🔖 3. Closing the Book

> "Basically, micro state management involves choosing the right solution and library for specific problems. To do micro state management, you need to understand what the problem is and what solutions are available for the problem. I hope this book helps developers find the right solutions."

### What Good Decision Making Is

- This sentence at the end of the book seems to be a good attitude to have when reading and applying this book
- When introducing specific technology stacks, the reason "~ uses ~ library" is not very good. "Somewhere uses ~ library for ~ reason, we also have ~ problem so let's solve it with ~ library" is better decision making

### Thoughts While Refactoring

- In existing code, all state was randomly using Context API, so things that didn't need to be managed globally were being managed globally
- The most important thing I focused on while refactoring was classifying what to manage globally (leave in Context) and what to manage locally. First by service unit, then by page unit, then component unit, function unit, .. and analyzed code by small state units and discussed with colleagues, gradually removing them from Context one by one
- After removing dependencies from Context, complex logic was simplified, modularized, and the component could be used anywhere, and even though we dramatically reduced code lines, all functionality worked normally. Even reduced unnecessary rendering, reducing flickering and improving speed. I struggled with this part for over a month, but when problems unraveled like untangling thread, I felt proud

### Now When Applying

- Now only the task of 'using better' the global states left using global state libraries remains
- I still haven't decided whether to use Zustand or Jotai, but even if I do use them, I should introduce libraries that can respond to (i.e., solve) the problems we face

### My Thoughts

This chapter provides a comprehensive comparison of the three main global state management libraries discussed in the book. The key insight is that each library has its own strengths and trade-offs, and the choice depends on the specific needs and constraints of your project.

The table comparison is particularly valuable - it shows how each library approaches the same problems differently, from Zustand's simplicity to Jotai's granularity to Valtio's intuitiveness.

The discussion about good decision making is important - it emphasizes that technology choices should be based on solving specific problems rather than following trends or copying other teams.

### Code Examples

```typescript
// ✅ GOOD: Zustand approach - simple and flexible
import create from 'zustand';

interface BearState {
  bears: number;
  fish: number;
  increasePopulation: () => void;
  addFish: (amount: number) => void;
}

const useBearStore = create<BearState>((set) => ({
  bears: 0,
  fish: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  addFish: (amount) => set((state) => ({ fish: state.fish + amount })),
}));

function BearCounter() {
  const bears = useBearStore((state) => state.bears); // Selector for optimization
  const increasePopulation = useBearStore((state) => state.increasePopulation);

  return (
    <div>
      <h1>{bears} bears around here...</h1>
      <button onClick={increasePopulation}>Add a bear</button>
    </div>
  );
}

// ✅ GOOD: Jotai approach - granular and composable
import { atom, useAtom } from 'jotai';

const countAtom = atom(0);
const nameAtom = atom('Ella');
const doubleCountAtom = atom((get) => get(countAtom) * 2);
const greetingAtom = atom((get) => `Hello, ${get(nameAtom)}!`);

function Counter() {
  const [count, setCount] = useAtom(countAtom);
  const doubleCount = useAtom(doubleCountAtom)[0];

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
  const greeting = useAtom(greetingAtom)[0];

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

// ✅ GOOD: Valtio approach - intuitive and mutable
import { proxy, useSnapshot } from 'valtio';

const state = proxy({
  count: 0,
  name: 'Ella',
  theme: 'light' as 'light' | 'dark',
});

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

// ✅ GOOD: Decision framework for choosing libraries
function chooseStateLibrary(requirements: {
  projectSize: 'small' | 'medium' | 'large';
  teamExperience: 'beginner' | 'intermediate' | 'expert';
  performanceNeeds: 'low' | 'medium' | 'high';
  debuggingNeeds: 'low' | 'medium' | 'high';
}) {
  const { projectSize, teamExperience, performanceNeeds, debuggingNeeds } =
    requirements;

  // Small projects with beginners
  if (projectSize === 'small' && teamExperience === 'beginner') {
    return 'valtio'; // Easy to learn and use
  }

  // Large projects with experts
  if (projectSize === 'large' && teamExperience === 'expert') {
    return 'jotai'; // Granular control and flexibility
  }

  // Medium projects or mixed teams
  if (projectSize === 'medium' || teamExperience === 'intermediate') {
    return 'zustand'; // Good balance of simplicity and power
  }

  // High performance needs
  if (performanceNeeds === 'high') {
    return 'jotai'; // Best granular optimization
  }

  // High debugging needs
  if (debuggingNeeds === 'high') {
    return 'zustand'; // Better DevTools support
  }

  return 'zustand'; // Default choice
}

// ✅ GOOD: Migration strategy between libraries
function migrateFromContextToLibrary(
  contextState: any,
  targetLibrary: 'zustand' | 'jotai' | 'valtio'
) {
  switch (targetLibrary) {
    case 'zustand':
      return create((set) => ({
        ...contextState,
        // Add setters for each state property
        updateState: (updater: (state: any) => any) => set(updater),
      }));

    case 'jotai':
      return {
        // Create atoms for each state property
        countAtom: atom(contextState.count),
        nameAtom: atom(contextState.name),
        // Add derived atoms as needed
      };

    case 'valtio':
      return proxy({
        ...contextState,
        // State can be mutated directly
      });
  }
}

// ✅ GOOD: Performance comparison
function PerformanceComparison() {
  // Zustand - manual optimization with selectors
  const zustandCount = useBearStore((state) => state.bears);

  // Jotai - automatic optimization with atoms
  const jotaiCount = useAtom(countAtom)[0];

  // Valtio - automatic optimization with snapshots
  const valtioSnap = useSnapshot(state);
  const valtioCount = valtioSnap.count;

  return (
    <div>
      <p>Zustand: {zustandCount}</p>
      <p>Jotai: {jotaiCount}</p>
      <p>Valtio: {valtioCount}</p>
    </div>
  );
}

// ✅ GOOD: Best practices summary
const bestPractices = {
  zustand: {
    useSelectors: true,
    keepStoresSmall: true,
    useEqualityFunctions: true,
    avoidNestedUpdates: true,
  },
  jotai: {
    createSmallAtoms: true,
    useDerivedAtoms: true,
    avoidLargeAtoms: true,
    useAtomFamily: true,
  },
  valtio: {
    useSnapshots: true,
    avoidDeepNesting: true,
    useActions: true,
    keepStateFlat: true,
  },
};
```
