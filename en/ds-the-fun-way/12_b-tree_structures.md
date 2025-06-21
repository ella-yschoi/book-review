# Data Structures the Fun Way - 12. B-Trees

> Extending the problem of accessing individual values to handle the cost of accessing data blocks, and B-trees, a new data structure that can handle such situations

<br/>

## 🔖 B-Tree Structure

### Overview

- Storing multiple pieces of data in one node allows you to extract all values within the same node by paying the expensive search cost only once.
- Once a node is stored in local memory, you can quickly access its values.
- B-trees provide a creative way to minimize search costs while combining indexing and keys.
- B-trees are also examples showing how to define tree operations to prevent trees from becoming heavily skewed.

### B-Tree Structure

- B-trees apply the multi-branching structure for storing individual keys seen in tries or quad trees.
- This means B-trees allow much more than 2 branches in internal nodes.
- B-trees are balanced data structures. All leaf nodes are at the same depth from the root.

<br/>

## 🔖 12.2 Searching in B-Trees

### Search Method

- An important difference between B-trees and binary search trees is that you may need to check more than one key at a node.
- At each node, scan through keys until you find the desired key or the first key with a value greater than the target.
- If you find a key greater than the target in an internal node, move to the appropriate child and continue searching.

<br/>

## 🔖 12.3 Inserting Keys

### Key Insertion Algorithm

- In the first step of the algorithm, recursively traverse the tree to find the position to insert the new key.
- During this process, if you find a matching key, you can update that key's data.
- If there's no matching key, go down to a leaf node and insert the key into the array.

<br/>

## 🔖 12.4 Removing Keys

### Key Removal Algorithm

- Removing keys is similar to adding them.
- First go down the tree, then if you find the key, remove the found key. Then go back up the tree, checking and repairing nodes that have too few keys.
- Since no node is removed except for removing empty root nodes, the tree can always maintain a balanced state.

<br/>

## 🔖 12.5 Why B-Trees are Important

- Shows how to adapt the operations of data structures we already know to handle cases where memory access to other nodes costs more than accessing content within nodes.
- B-trees operate by combining indexing and storage to minimize the number of necessary accesses. This is important for large datasets stored on disks or external servers.
- And B-trees continuously reorganize their structure to maintain balance according to the distribution of stored data.
