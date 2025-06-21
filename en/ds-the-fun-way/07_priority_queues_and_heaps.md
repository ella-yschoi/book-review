# Data Structures the Fun Way - 7. Priority Queues and Heaps

> Data structures for finding priority-specified items in sets: priority queues, and heaps, the most common data structure for implementing this useful tool

<br/>

## 🔖 7.1 Priority Queues

### Comparing Queues and Heaps

- Priority queues are data structures designed to store a set of items and efficiently find and remove the item with the highest priority
- e.g. When processing network requests, each packet can be given an explicit priority. In this case, priority queues can be used to process high-priority requests first.
- When implementing priority queues, there are ways to use sorted lists and ways to use unsorted lists. However, both approaches may have efficiency disadvantages.
- Sorted lists are slow for insertion operations. Unsorted lists are slow for finding high-priority items.
- At this time, using heaps can improve the efficiency of priority queues. This is because heaps can handle both insertion and deletion operations with log time complexity. In particular, max heaps or min heaps are effective for implementing priority queues.

<br/>

## 🔖 7.2 Max Heap

### What is a Max Heap?

- A variation of binary trees that maintains a special order between nodes and their children. Max heaps store elements according to the max heap property.
- What is the max heap property? It means that all node values in the tree are greater than or equal to their child node values.

### Using Max Heaps

- Users can efficiently use the largest element
- Can efficiently remove the largest element
- Can efficiently add arbitrary elements

### Adding Elements to Heap

- All ancestor nodes of the newly added node must have higher priority than the added node.
- To add a new element to the heap, you must add the element to the first empty space at the bottom of the tree.

### Removing the Largest Element from Heap

- For example, storing a list of pending network requests and processing high-priority requests
- First, you must break the heap property and then restore it

<br/>

## 🔖 7.3 Updating Priority

### Processing Method

- When increasing the value of an item, you must restore the heap property by moving the item upward in the max heap.
- By separately dividing the code for moving elements up or down, the same code can be utilized for updating when adding or removing maximum values

<br/>

## 🔖 7.4 Min Heap

### What is a Min Heap?

- A heap for easily finding items with the lowest value
- For example, instead of sorting network packets by priority, sort them by arrival time and process packets that arrived first
- Theoretically, you can continue using max heaps by just changing the sign of values, but completely solve the problem by adding small modifications to the heap property

<br/>

## 🔖 7.5 Heap Sort

### What is Heap Sort?

- An algorithm that sorts a list of elements using the heap data structure
- Input is an unsorted array, and output is an array containing the same elements but sorted in descending order

### Steps of Heap Sort

- Build a max heap from all items
- Extract all items from the heap in descending order and store them in an array

### Time Complexity

- Like insertion, in the worst case, execution time is proportional to Nlog<sub>2</sub>(N)
- Each extraction takes up to log<sub>2</sub>(N) to restore the heap property
- To get a sorted list, all N items must be extracted.
