# Data Structures the Fun Way - 1. Memory Information

> Basic methods for storing data in memory

<br/>

## 🔖 1.1. Variables

### Variables are Labels

- Variables are like paper labels attached to folders containing documents. After attaching a label, there's no need to remember the order or position of the folder.
- Therefore, it's important to use names that contain sufficient information.
- Variables are also related to the type of stored data.

<br/>

## 🔖 1.2 Composite Data Structures

### When Things Get Complex, We Need to Bundle Them

- Composite data structures refer to structures or objects that tie multiple individual variables into one group.
- A business card is a data package containing multiple pieces of information such as name, email, etc. (Great analogy!)

<br/>

## 🔖 1.3 Arrays

### Characteristics Unique to Arrays

- Since the boxes within an array are adjacent to each other in computer memory, each box can be easily accessed by calculating the offset from the first element and reading the memory at the corresponding location.
- This means that regardless of the position of the box you want to access, only one addition and memory access is needed.
- That's why arrays are particularly convenient for storing items with order.
- Insertion sort sorts a portion of the array and expands this sorted range until the entire array is sorted. The algorithm iterates through each element of the unsorted array, moving them to the correct position in the sorted portion.

### Connecting with Book Content

- It's also possible to start indices from 1, and some programming languages follow this rule. This is a newly learned fact! Notably, R is said to start indices of vectors or lists from 1. However, languages like C, Python, and JavaScript use 0-based indexing as default. This is said to originate from the computer engineering tradition where arrays were designed based on memory addresses.
- The book says that arrays are not like bookshelves where you can insert books, but rather like stores lined up in a row. So that's why arrays are sometimes called inefficient data structures?

### Insertion Sort

- Insertion sort sorts a portion of the array and expands this sorted range until the entire array is sorted. In other words, insertion sort initially creates a small sorted portion and gradually expands this sorted range by adding new elements one by one. This way, the entire array eventually becomes sorted.
- Therefore, at the point when iteration i starts, all elements at positions i-1 and below are already sorted.

### Insertion Sort vs. First-In-First-Out

- The book used the example of sorting coffee by freshness. So what's the difference from First-In-First-Out (FIFO)?
- Insertion sort is not simply sorting in the order they came in, but rather a strategy closer to maintaining "expiration date order" overall by finding the appropriate place within the already sorted section and inserting it each time a new product comes in.
- To summarize, First-In-First-Out follows the incoming order as is, while insertion sort is a sorting method that repositions according to criteria each time a new element is inserted.

### Implementing Insertion Sort with Nested Loops

```typescript
function insertionSort(A: number[]) {
  const N = A.length;
  let i = 1; // Assume 0 is already sorted

  while (i < N) {
    let current = A[i]; // Store the element to insert temporarily
    let j = i - 1; // Start comparison from the element right before current

    while (j >= 0 && A[j] > current) {
      A[j + 1] = A[j]; // Move j-th element one position back
      j = j - 1; // Move j one position forward (to compare with earlier elements)
    }

    A[j + 1] = current; // Place current at the position after movement ends
    i = i + 1; // Increment i for next position
  }

  return A;
}
```

### Time Complexity

- The time complexity of insertion sort is O(N²) on average.
- If it's already almost sorted, the best case can be faster at around O(N), but in the worst case and average case, it performs approximately N² comparisons and movement operations.
- Although not very efficient, as the book says, it's a sorting algorithm that helps understand how to handle arrays.
- The characteristics of arrays learned through insertion sort can be summarized in three points: accessing elements by index, exchanging values when inserting new elements, and being able to iterate through all elements.

<br/>

## 🔖 1.4 Strings

### Relationship Between Strings and Arrays

- Strings are often thought of as a special type of array, an ordered list of characters.
- Since strings internally have a structure where characters are arranged in order, they can be viewed as "arrays of characters." (Of course, actual implementation or characteristics differ)
- To summarize, both `str[0]` and `arr[0]` are similar in that they "access ordered data using indices," but to say they are identical, there are implementation differences depending on data types and languages.

```javascript
const arr = [1, 2, 3, 4];
const str = 1234;

console.log(arr[1]); // 2
console.log(str[1]); // undefined
```

### Algorithm for Checking Equality of Two Strings

```typescript
function stringEqual(str1: string, str2: string): boolean {
  // If the lengths of the two strings are different, return false immediately
  if (str1.length !== str2.length) {
    return false;
  }

  // If the lengths are the same, compare each character
  for (let i = 0; i < str1.length; i++) {
    // If any character is different, return false
    if (str1[i] !== str2[i]) {
      return false;
    }
  }

  // If all characters are the same, return true
  return true;
}
```

### Time Complexity

- In the best case, if the first character is different, it ends with one comparison, so it's O(1).
- However, on average or in the worst case, all characters must be compared, so it's O(N).

### Array vs. String

So when solving algorithm problems, which is more efficient between arrays and strings? It seems appropriate to choose the right data structure according to the problem situation.

| **Category**        | **Array**                                                                                                                                                                                                                                                                   | **String**                                                                                                                                                                                                                                                                  |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Advantages**      | • **Random Access (O(1))**: Can immediately access elements at desired positions using indices <br/> • **Flexibility**: Can store various data types <br/> • **Various Operations Support**: Can efficiently implement various algorithms like insertion, deletion, sorting | • **Special Operations Support**: Provides string-specific functions like substring, split, join, etc. <br/> • **Immutability**: Can be safely shared <br/> • **Text Processing Optimization**: Advantageous for text processing like pattern matching, regular expressions |
| **Disadvantages**   | • **Fixed Size**: In some languages, size is fixed, requiring new array creation when changing <br/> • **Memory Usage**: Requires contiguous memory, large arrays may waste memory                                                                                          | • **Difficulty in Modification**: If immutable, requires creating new string when modifying <br/> • **Limited Data Types**: Mainly stores characters only, cannot store various types                                                                                       |
| **Time Complexity** | • **Access and Modification**: Random access and modification are fast (O(1)) <br/> • **Insertion/Deletion**: Takes O(N) when inserting/deleting in the middle                                                                                                              | • **Access**: Index access is O(1), but modification requires copying entire string (O(N)) <br/> • **Search/Pattern Matching**: Efficient when using optimized algorithms (generally O(N))                                                                                  |
| **Efficiency**      | • **Data Manipulation**: Advantageous for general data manipulation like sorting, insertion, deletion <br/> • **Various Types**: Suitable when dealing with multiple data types                                                                                             | • **Text Processing**: Optimized for text-related tasks like search, pattern matching <br/> • **String-Specific Function Utilization**: Easy to implement string-related features                                                                                           |
| **Memory**          | • **Flexible Type Storage**: Can store various types, memory usage is dynamic <br/> • **Contiguous Memory**: Large arrays may waste memory                                                                                                                                  | • **Fixed Character Type**: Stores only characters, memory usage is constant <br/> • **Immutability**: Additional memory usage when modifying strings                                                                                                                       |

<br/>

## 📚 Related Problems and Solutions

- [1768. Merge Strings Alternately Problem](https://leetcode.com/problems/merge-strings-alternately/description/?envType=study-plan-v2&envId=leetcode-75)
- [1768. Merge Strings Alternately Solution](https://github.com/ella-yschoi/algorithm-probs/blob/main/LeetCode/Easy/1768_Merge_Strings_Alternately.js)
