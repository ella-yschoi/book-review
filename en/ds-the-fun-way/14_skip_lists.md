# 📌 Data Structures the Fun Way - 14. Skip Lists

> Summary: Skip List is a data structure designed to allow fast search in sorted linked lists by enabling some elements to be skipped, providing near O(log n) search performance without tree structures, and can be used as an alternative to balanced binary search trees (BST).

<br/>

## 🔖 14.1. What is a Skip List?

### Problems with Linked Lists

- Since nodes in linked lists are connected sequentially, to find a specific element, you need to search from beginning to end
- Therefore, search speed is slow at O(n)

### Core Idea of Skip Lists

- Improves search speed by adding "skip pointers" that skip some nodes
- Each node can have multiple levels, and higher levels include pointers that can move further
- As a result, search, insertion, and deletion operations have average time complexity of O(log n)

<br/>

## 🔖 14.2. How Skip Lists Work

### Structure

- Skip lists can be viewed as sorted linked lists with multiple levels
- The lowest level (level 1) is the same as a regular linked list
- As you go to higher levels, only some nodes are included, allowing faster skipping and searching

### Search Process (Search)

1. Start from the top level (highest level)
2. Compare the next node's value with the target value
   - If greater than target value, move to lower level
   - If less than target value, move to the right
3. When reaching the lowest level, you find the answer

📌 Search speed: Average O(log n)

<br/>

### Insertion Process (Insert)

1. Search for the position to insert the new node
2. Probabilistically determine the level (e.g., 50% probability of going up to higher level)
3. Connect new pointers according to the selected level

📌 Insertion speed: Average O(log n)

<br/>

### Deletion Process (Delete)

1. While searching for the node to delete, find pointers pointing to that node
2. Remove the node at each level and reconnect pointers

📌 Deletion speed: Average O(log n)

<br/>

## 🔖 14.3. Skip List Execution Time

- Search, insertion, and deletion all have average performance of O(log n)
- In the worst case (when all nodes have only one level), it can be O(n), same as regular linked lists
- However, by utilizing randomization, it can maintain performance similar to binary search trees in most cases

<br/>

## 🔖 14.4. Skip List vs. Binary Search Tree

| Comparison Item               | Skip List                               | Binary Search Tree (BST)                          |
| ----------------------------- | --------------------------------------- | ------------------------------------------------- |
| **Structure**                 | Multiple levels of linked lists         | Tree structure (left/right children)              |
| **Search Speed**              | Average O(log n)                        | Average O(log n)                                  |
| **Worst Case**                | O(n) (if levels are poorly distributed) | O(n) (if balance is lost)                         |
| **Implementation Difficulty** | Relatively easy                         | Need to maintain balance like AVL, Red-Black Tree |
| **Randomization Usage**       | Yes                                     | No                                                |

👉 Skip lists can be used as an alternative to trees that don't require balance maintenance

<br/>

## 🔖 14.5. Use Cases of Skip Lists

- Database indexing: Used when fast search is needed
- Cache systems: Useful for quickly processing recent data searches
- Distributed systems (e.g., LevelDB, Redis): Maintain O(log n) performance while storing data

<br/>

## 🎯 Key Summary of Skip Lists

- Data structure that introduces "skipping" concept to improve slow search speed of linked lists
- Search, insertion, and deletion speeds are average O(log n)
- Similar performance to balanced trees (BST) but doesn't require balance maintenance
- Can prevent worst cases using randomization
- Widely used in databases, caching, and distributed systems
