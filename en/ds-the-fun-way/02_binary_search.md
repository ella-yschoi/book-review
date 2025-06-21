# Data Structures the Fun Way - 2. Binary Search

> Algorithm for quickly finding specific values in sorted lists

<br/>

## 🔖 Linear Scan

### What is Linear Scan? And Its Pros and Cons

- It's a method of finding the target value by comparing elements one by one from the beginning to the end of the list.
- It has the advantage of simple implementation and can be used even if the array is not sorted.
- However, if the array size is large, efficiency may decrease, so it's good to consider other methods (like binary search) at this time.
- The book also calls it 'brute force search'.

### Implementation in JavaScript

```javascript
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) {
      return i;
    }
  }

  return -1;
}
```

### Time Complexity

- Best: When the target value is at the first position of the array, only one comparison is needed, so O(1)
- Worst: When the target value is at the last position or not in the array, all elements must be compared, so O(N)

<br/>

## 🔖 Binary Search Algorithm

### What is Binary Search?

- It's an algorithm for finding the target value in a 'sorted' list. It divides the list in half and determines which half the target value belongs to. Then it discards the half that doesn't contain the target value and repeats the same process with only the remaining half that could still contain the target value. This is repeated until only the last value remains.
- In binary search, it utilizes the fact that the array is sorted in ascending order.

### Why the Array Must Be Sorted Before Performing Binary Search

- As mentioned earlier, binary search is an algorithm that works correctly only on sorted arrays. If the array is not sorted, even if you perform binary search, you cannot guarantee correct results.
- You can easily sort the array using JavaScript's built-in `sort()` method.

### Implementation in JavaScript

```javascript
function binarySearch(arr, target) {
  arr.sort((a, b) => a - b);

  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      return mid; // Target value found
    } else if (arr[mid] < target) {
      left = mid + 1; // Search right half
    } else {
      right = mid - 1; // Search left half
    }
  }

  return -1;
}
```

### Time Complexity

- `sort` function: Generally O(N log N)
- Binary search: O(log N)
- Overall: When sorting the array and performing binary search, O(N log N)

<br/>

## 🔖 Linear Search vs. Binary Search

### In Terms of Time Complexity

- In the worst case of linear scan, execution time is 'linearly' proportional to data size.
- Binary search, even in the worst case, removes half the data at each step, so the number of comparisons is logarithmic with respect to the size of the dataset. (e.g., when N = 16, maximum 4 comparisons needed: log₂16 = 4)
- The advantage of only needing to compare as many times as the logarithm of the number of elements for sufficiently large lists offsets the cost incurred at each step. In other words, even in the worst case, execution time increases slowly as data size increases (compared to linear), making it still efficient.
