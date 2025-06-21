# Data Structures the Fun Way - 3. Dynamic Data Structures

> Dynamic data structures that change structure when data changes

<br/>

## 🔖 3.1 Limitations of Arrays

### Are Arrays Static?

- The book uses the term 'array' to refer simply to static arrays. Of course, compared to dynamic data structures that can change depending on the application, they are less dynamic, but remember that arrays can also be dynamic depending on the implementation method.
- For example, in JavaScript, arrays change size dynamically according to `push()` or `pop()`. Therefore, it's difficult to say that arrays are absolutely static.
- Like this, while arrays can add/delete elements, memory usage can be unpredictable, and most importantly, the time complexity can become large in the process of reallocating the array and copying elements.

### Limitations of Arrays as Described in the Book

- It's the fundamental limitation of data structures with fixed memory layout, i.e., fixed size.
- Such fixed data structures cannot increase the size of the data structure according to data. Due to this limitation, several common operations become expensive.
- Array size is fixed when creating the array, so to expand the array to store more data, you need to create a new larger memory block. This is a significant additional cost.

### Why Arrays Cannot Easily Insert New Items in the Middle

- Arrays are stored in contiguous spaces in memory. As mentioned in the previous chapter, each element of the array is stored in adjacent memory locations, allowing fast access through fixed indices.
- To insert an element in the middle, existing elements must be pushed aside. Therefore, it has O(N) time complexity.
- While linked lists can be O(1) by adjusting pointers between nodes without needing data movement, arrays must push elements aside due to their contiguous memory structure.

<br/>

## 🔖 3.2 Pointers and References

### The Concept of Pointers

- I didn't understand the pointer concept well before. And I thought, why not just store "I'm here" information in variables? But the analogy in the book is perfect.
- Instead of leaving various documents from the project folder in an open space, you leave a memo to colleagues saying "It's in the second file cabinet in the 3rd floor records room." This is the role of pointers.
- Also, this single address can be shared with multiple colleagues, eliminating the need to make complete copies of files.
- This pointer provides a mechanism for connecting memory blocks used in linked lists.

<br/>

## 🔖 3.3 Linked Lists

### Linked Lists Similar to Arrays

- Linked lists are data structures that store multiple values, just like arrays.
- However, unlike arrays, linked lists consist of a chain of nodes connected by pointers.
- The basic node of a linked list consists of a value that can contain any type of value, and a pointer that points to the next node in the list.
- Since each element stores both value and pointer, it uses more memory than arrays. But the flexibility provided by pointers is worth the increased memory usage.
- And unlike arrays, nodes don't need to be adjacent. In other words, thanks to pointers, they don't need to be located in contiguous memory blocks.

<br/>

## 🔖 3.4 Operations on Linked Lists

### Hmm...

- This part is difficult to summarize, so it would be better to read the book directly.
