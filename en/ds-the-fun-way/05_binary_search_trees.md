# Data Structures the Fun Way - 5. Binary Search Trees

> BST creating dynamic data structures based on the concept of binary search algorithm

<br/>

## 🔖 5.1 Binary Search Tree Structure

### Book's Explanation of Trees

- A tree is a hierarchical data structure composed of branching chains of nodes.
- Tree structure is a natural extension of linked lists, differing from linked lists in that tree nodes allow two next nodes pointing to disjoint sublists.

### Simply Put, What is a Tree?

- A tree is a data structure that stores data hierarchically. In other words, think of it as a structure with branches that extend in multiple directions.

### Differences from Linked Lists

- While linked lists have nodes pointing to only one next node, trees can have each node pointing to multiple child nodes.
- In binary trees, the most common tree form, each node can point to a maximum of 2 child nodes.
- To analogize, if linked lists are like train cars connected in a line where data is connected in only one direction, trees are similar to trees that branch from roots to branches.

### Nodes

- Can contain different information as needed. For example, you can store a pointer pointing to the parent in the node.
- Storing pointers is useful not only for tree traversal but also when removing nodes.
- Node values are like signs telling you that rooms 500-519 are on the left and rooms 520-590 are on the right when finding a hotel room. (Great analogy!)
- That is, with just one simple check, you can choose the appropriate direction and ignore rooms in other directions.

<br/>

## 🔖 5.2 Searching in Binary Search Trees

### Iterative Search

Uses a `while` loop instead of recursive calls and repeatedly goes down the tree.

```javascript
function createNode(value) {
  return {
    value,
    left: null,
    right: null,
  };
}

function insertNode(root, value) {
  if (!root) return createNode(value);

  let current = root;
  while (true) {
    if (value < current.value) {
      if (!current.left) {
        current.left = createNode(value);
        break;
      }
      current = current.left;
    } else {
      if (!current.right) {
        current.right = createNode(value);
        break;
      }
      current = current.right;
    }
  }

  return root;
}

function iterativeSearch(root, value) {
  let current = root;
  while (current) {
    if (value === current.value) return true; // Value exists
    current = value < current.value ? current.left : current.right;
  }
  return false; // Value doesn't exist
}
```

### Recursive Search

The search function is initially called with the root node of the tree and calls itself using the next node.

```javascript
function insertNodeRecursive(root, value) {
  if (!root) return createNode(value);

  if (value < root.value) {
    root.left = insertNodeRecursive(root.left, value);
  } else {
    root.right = insertNodeRecursive(root.right, value);
  }

  return root;
}

function recursiveSearch(root, value) {
  if (!root) return false; // Value doesn't exist
  if (value === root.value) return true; // Value exists

  return value < root.value
    ? recursiveSearch(root.left, value)
    : recursiveSearch(root.right, value);
}
```

### Comparison Summary

| Method          | **Iterative Search**                     | **Recursive Search**                                                   |
| --------------- | ---------------------------------------- | ---------------------------------------------------------------------- |
| Code Complexity | Relatively simple with clear flow        | Intuitive with function calls but risk of stack overflow in deep trees |
| Performance     | Memory efficient as it doesn't use stack | Additional memory usage due to recursive calls                         |
| Use Cases       | Suitable when tree depth is deep         | Suitable when code readability and conciseness are important           |

<br/>

## 🔖 5.3 Modifying Binary Search Trees

### Logic Simplification

- To simplify logic using binary search trees, you can wrap the entire tree in a thin data structure that includes the root node.
- Such wrapper functions may seem wasteful but make tree usage easier and greatly simplify root node handling.

### Node Insertion

- Inserting values into a binary search tree uses the same algorithm as searching.
- When allowing duplicates, it continues until reaching a dead end, then inserts the new value into the tree.
- When not allowing duplicates, you can replace data stored in nodes matching the new value or store additional information.
- The cost of inserting a new node into the tree is proportional to the depth of the path where the new node is inserted.
- In the worst case, insertion cost increases linearly with tree depth.

### Node Removal

- It's a more complex process than insertion. The work becomes more complex as the number of children increases.
- When removing a leaf node, you must remove that node and update the parent's child pointer to null to indicate that the node no longer exists.
- Internal nodes with only one child promote the child node by changing pointers and promote that child one level to remove it.
- Since the promoted node was already part of the parent's subtree, all its sub-items continue to maintain binary search tree properties.
- You must ensure that the integrity of the remaining tree excluding the node to be removed is maintained and continues to follow binary search tree properties.
- To remove an internal node with two children, first exchange the successor node to that position.

<br/>

## 🔖 5.4 Unbalanced Trees

### Perfect Balance

- Tree depth increases by 1 each time the number of nodes is doubled.
- In the worst case, performance increases proportionally to O(log<sub>2</sub>N).

### Unbalanced Cases

- Tree depth can increase linearly with the number of elements. O(N)
- Of course, there are methods like red-black trees to maintain balance when dynamic insertion and removal occur, but there's a trade-off of increasing tree operation complexity instead of maintaining balance.

<br/>

## 🔖 5.5 Bulk Construction of Binary Search Trees

### Creating Balanced Binary Search Trees

- You can easily construct a binary search tree by repeatedly adding nodes. However, unbalanced trees may be created.
- Therefore, create a balanced binary search tree by recursively dividing elements from a sorted array into smaller subsets.
