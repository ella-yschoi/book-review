# Data Structures the Fun Way - 6. Tries and Adaptive Data Structures

> Data structures optimized by additionally considering the characteristics of strings in binary search trees

<br/>

## 🔖 6.1 Binary Search Trees Made of Strings

### Limitations of Binary Search Trees

- Binary search trees can store anything that can be sorted in order.
- At first glance, binary search trees seem to provide a simple and efficient mechanism for searching string data.
- However, you need to pay attention to the cost of each comparison.
- In the worst case, the cost of string comparison operations is proportional to the length of the string itself.

### Cost and Limitations of String Comparison

- Sequential comparison of strings in binary search trees is more expensive than comparison of two numbers.
- In many languages, there's also the point that there aren't many characters that can be included at each position of a string.

<br/>

## 🔖 6.2 Tries

### What is a Trie?

- It's a tree-based data structure that divides strings into different subtrees based on prefixes.
- Instead of dividing data into two sets at each node, tries divide tree branches based on prefixes so far.
- It emerged to improve file searching on computers with slow memory access speeds.

### Characteristics of Tries

- Each node in a trie represents the prefix of the string compared so far.
- Like binary search trees, tries also start from the root node, and each node can have two or more children.
- Tries perform dramatic multiple branching at all nodes.
- Unlike binary search trees, not all nodes in tries represent items.
- Useful information such as the prefix of the current node can be stored in trie nodes.

### Searching in Tries

- It's efficient because you don't need to compare the entire string or the front part of the prefix.
- When implementing in code, you can track by passing an index representing the position of the character to check at the current step to the recursive function.
- Using a Trie wrapper can simplify by hiding the reference to the root node and the counter needed for the recursive function.
- Unlike binary search trees, the number of comparisons in successful trie searches is independent of the number of strings stored in the trie.

### Trie Search Code

```javascript
function TrieNodeSearch(current, target, index) {
  if (index === target.length) {
    // ①
    if (current.isEntry) {
      return current;
    } else {
      return null;
    }
  }

  const nextLetter = target[index]; // ②
  const nextIndex = LetterToIndex(nextLetter); // ③
  const nextChild = current.children[nextIndex]; // ④

  if (nextChild === null) {
    return null;
  } else {
    return TrieNodeSearch(nextChild, target, index + 1);
  }
}

// LetterToIndex is a function that converts a specific character (nextLetter) to an index number
// current represents the current node, target is the string to search, and index is the position of the string currently being searched
```

### Adding and Removing Nodes

- Tries are also dynamic data structures that accurately represent data changes, like binary search trees.
- Unlike insertion in binary search trees, multiple nodes can be added while inserting one item.

<br/>

## 🔖 6.3 Why Tries are Important

### Benefits of Tries

- Tries become more efficient as more strings are added and the number of strings with common prefixes increases.
- Since string comparison itself is costly, you can gain great benefits by checking and comparing based on common prefixes.

### Examples of Trie Usage

- Data structures for tracking structural labels such as serial numbers, model numbers, etc., made of short alphabets and numbers
- Even with tens of billions of products, storage space can be saved by utilizing prefixes of serial numbers.
- The key point is that when optimizing operation costs, you can better utilize the structure of strings.
