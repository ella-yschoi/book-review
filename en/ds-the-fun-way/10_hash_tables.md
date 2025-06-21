# Data Structures the Fun Way - 10. Hash Tables

> Dynamic data structures optimized for insertion and search

<br/>

## 🔖 10.1 Storage and Search Using Keys

### Hash Table Mechanism

- Pre-allocating each index of an array for all possible values is too memory-intensive and inefficient. (e.g., to store 1 billion numbers, you need an array of size 1 billion)
- Instead, it's more efficient to find storage location by converting keys according to specific rules when storing values
- At this time, hash functions are used to convert keys to specific hash values, and those values are utilized as array indices
- Hash functions serve to convert input values to fixed-size numbers (hash values), enabling fast data search
- To summarize, hash tables are data structures that operate in the "key → hash function → index conversion → value storage" manner.

<br/>

## 🔖 10.2 Hash Tables

### Role of Hash Tables

- Compresses key space through mathematical mapping.
- Reduces large key space to smaller regions through hash functions that map original keys to table positions (hash values).
- Since hashing can map non-integer values to integer ranges, it can solve problems that arise when keys are not integers.

### Collisions

- For mathematical functions that map large sets of keys to smaller sets of hash values, collisions are sometimes inevitable.
- At this time, you can mitigate collisions by increasing hash table size or choosing better hash functions, but if key space is larger than the number of boxes, it's difficult to completely eliminate collisions.
- Therefore, methods are needed to properly handle two or more data trying to occupy the same position.
- Methods for handling collisions in hash tables include chaining and linear probing.

### Chaining

- Chaining is a method of handling hash table collisions using additional structures inside each box.
- Instead of storing fixed data or pointers to single data in each box, it stores a pointer pointing to the 'head' of a linked list.
- The advantage is that you don't scan linked lists for all elements. You only scan the linked list when there are elements with matching hash values.
- In other words, instead of searching through a large list containing all elements, you only need to search through the small list in the box corresponding to a specific hash value.

### Linear Probing

- Another way to handle collisions is to use adjacent boxes.
- If you need to insert data into a box that already has another key, you move to the next box and examine that box.
- Continue repeating until you find an empty box or a box with data having the same key.
- In other words, linear probing starts from the index corresponding to the hash value of the key and continues until you find the key you're looking for or conclude that it can't be found.
- However, hash tables using linear probing don't use linked lists in each box, so they use wrapper data structures to store key-value combinations.
- The advantage of linear probing is that it better utilizes the array initially allocated by the hash table and doesn't incur additional costs of linked list traversal at the box level.
- The disadvantage is that you need to examine more elements while looping, and each element's key may not match the key of the target you're searching for.

<br/>

## 🔖 10.3 Hash Functions

### Good Hash Functions

- The difference between good and bad hash functions produces effects similar to the difference between hash tables and linked lists.
- Hash functions must map the same key to the same hash box every time. If this doesn't work, you might fail to search even though you've already inserted an item into the hash table.
- Hash functions must map all keys to a limited range, and this range corresponds to the number of boxes in the hash table.

### Uses of Hash Tables

- Hash tables are particularly useful when tracking sets of elements.
- Hash tables provide good mechanisms for storing already visited sets in BFS and DFS.
