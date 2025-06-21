# Data Structures the Fun Way - 4. Stacks and Queues

> Data structures that can read data according to the order in which data was stored

<br/>

## 🔖 4.1 Stacks

### Implementing Stack with Array

- When adding data, the stack can be expanded, but there may be additional costs when the array size needs to be expanded.
- JavaScript arrays have dynamic size, so `push()` operations are performed quickly if the array size is sufficient, but memory reallocation costs occur when the array size needs to be expanded.
- When `pop()`ing items from the stack, the internal operation is simpler than `push()`.
- `push()` inserts data at the end of the array and may need to expand memory considering the array size, but `pop()` is a simple operation of removing the last element of the array.

```javascript
const stack = [];

stack.push(10); // Add (push)
stack.push(20);
console.log(stack); // [10, 20]

stack.pop(); // Remove (pop)
console.log(stack); // [10]
```

### Implementing Stack with Linked List

- Using linked lists eliminates size limitations and eliminates the need to worry about memory reallocation.
- When `push()`ing items to the stack, a new node is added to the front of the linked list.
- A new node is created, and the new node's `next` pointer and the stack's `head` pointer are updated to insert the new node at the front of the list.
- When `pop()`ing, the value of the `head` node is returned, and the `head` pointer is moved to the next node.

```javascript
function createNode(value) {
  return { value, next: null };
}

function createStack() {
  let head = null;

  return {
    push(value) {
      const newNode = createNode(value);
      newNode.next = head;
      head = newNode;
    },
    pop() {
      if (!head) return null;
      const poppedValue = head.value;
      head = head.next;
      return poppedValue;
    },
    peek() {
      return head ? head.value : null;
    },
  };
}

const stack = createStack();
stack.push(10);
stack.push(20);
console.log(stack.peek()); // 20
console.log(stack.pop()); // 20
console.log(stack.pop()); // 10
```

<br/>

## 🔖 4.2 Queues

### Implementing Queue with Array

- Queues can be implemented using `push()` and `shift()` of arrays.
- However, `shift()` moves all elements of the array forward, so it's costly.
- To solve this disadvantage, using a **circular queue** can work efficiently without additional operations of moving from the end to the front of the array.

```javascript
const queue = [];

queue.push(10); // Add (enqueue)
queue.push(20);
console.log(queue); // [10, 20]

queue.shift(); // Remove (dequeue)
console.log(queue); // [20]
```

### Implementing Queue with Linked List

- Using linked lists allows dynamic management of queue size.
- Maintains a pointer pointing to the head of the list and a pointer pointing to the tail.
- `enqueue()` adds a new node to the tail, and `dequeue()` removes a node from the head.
- Operations are always performed in constant time (O(1)) regardless of queue size.

```javascript
function createQueue() {
  let head = null;
  let tail = null;

  return {
    enqueue(value) {
      const newNode = createNode(value);
      if (!tail) {
        head = tail = newNode;
      } else {
        tail.next = newNode;
        tail = newNode;
      }
    },
    dequeue() {
      if (!head) return null;
      const dequeuedValue = head.value;
      head = head.next;
      if (!head) tail = null;
      return dequeuedValue;
    },
    peek() {
      return head ? head.value : null;
    },
  };
}

const queue = createQueue();
queue.enqueue(10);
queue.enqueue(20);
console.log(queue.peek()); // 10
console.log(queue.dequeue()); // 10
console.log(queue.dequeue()); // 20
```

<br/>

## 🔖 4.3 Importance of Order

### Depth-First Search (DFS)

- A method of proceeding along one path while exploring deeper until reaching a dead end
- When reaching a dead end, it returns to the last visited branch and checks the next route.
- Uses a stack to maintain a list of possibilities to explore forward, exploring the most recently inserted possibility first.

```javascript
function dfs(graph, start) {
  const visited = new Set();
  const stack = [start];

  while (stack.length > 0) {
    const node = stack.pop();

    if (!visited.has(node)) {
      visited.add(node);
      console.log(node);
      for (const neighbor of graph[node]) {
        stack.push(neighbor);
      }
    }
  }
}

const graph = {
  A: ['B', 'C'],
  B: ['D', 'E'],
  C: ['F'],
  D: [],
  E: [],
  F: [],
};

dfs(graph, 'A'); // A, C, F, B, E, D
```

### Breadth-First Search (BFS)

- Explores in a similar way to depth-first search, but uses a queue to store future possibilities.
- At each step, exploration searches the possibility that has waited the longest, branching out in multiple different directions at the same depth before exploring deeper levels.

```javascript
function bfs(graph, start) {
  const visited = new Set();
  const queue = [start];

  while (queue.length > 0) {
    const node = queue.shift();

    if (!visited.has(node)) {
      visited.add(node);
      console.log(node);
      for (const neighbor of graph[node]) {
        queue.push(neighbor);
      }
    }
  }
}

bfs(graph, 'A'); // A, B, C, D, E, F
```

### Summary

- DFS and BFS both operate at the same speed in that they explore one possibility at a time.
