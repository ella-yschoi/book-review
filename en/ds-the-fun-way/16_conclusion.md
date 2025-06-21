# 📌 Data Structures the Fun Way - 16. Conclusion

> Book wrap-up!

<br/>

## 🔖 16.1 The Impact of Data Structure

- Adding just a little structure to data can dramatically change algorithm performance.
- Data structures help us access values quickly, efficiently combine multiple values, or reduce unnecessary searches.
- For example, binary search trees, tries, quadtrees, and k-d trees allow us to prune unnecessary paths during search.
- Tree-based data structures can exclude large portions of complex search spaces with simple condition checks.

<br/>

## 🔖 16.2 The Need for Dynamic Data Structures

- Dynamic data structures can change size flexibly, allowing for adaptable approaches.
- Since we don't need to pre-allocate memory with a fixed size, we can reduce memory waste.
- However, we need to connect scattered memory blocks with pointers, which can make the structure complex.
- Access speed and operation performance can vary depending on the pointer connection method.

<br/>

## 🔖 16.3 Amortized Cost

- When deciding whether to use a data structure, we must consider both the construction cost and the benefits we can gain.
- For example, sorting an array or creating a binary search tree has high initial cost, but subsequent repeated searches are much more efficient.
- On the other hand, if we only need to find a value once, a simple sequential search might be better than creating a complex data structure.
- If we need to search data repeatedly, it's worth paying the initial cost.

<br/>

## 🔖 16.4 Adapting Data Structures to Specific Problems

- Basic data structures provide a foundation that can be applied to various problems.
- Like spatial data structures, modifying or combining data structures can provide more specialized performance for specific situations.
- Ultimately, data structures are tools that can be continuously evolved to fit problem-solving needs.

<br/>

## 🔖 16.5 Trade-offs Between Memory and Execution Time

- We can approach problems by pre-storing calculation results or using more memory to increase operation speed.
- For example, using a heap allows us to quickly find the largest or smallest values.
- Also, in spatial partitioning trees, pre-calculating and storing the range of each node can reduce unnecessary searches.
- Ultimately, this can be seen as a **strategy of using more memory to save time**.

<br/>

## 🔖 16.6 Data Structure Tuning Methods

- Some data structures have **parameters that affect performance**.
- How these parameters are adjusted can significantly change performance.
- For example, in nearest neighbor search, the cell size of a grid affects precision and performance.
- In B-trees, we can improve cache efficiency by appropriately adjusting node size.
- It's important how well the internal settings of the data structure match the characteristics of the problem.

<br/>

## 🔖 16.7 The Impact of Randomization on Expected Behavior

- Some problems can have extremely different performance depending on input data.
- In such cases, introducing randomness into data structures or algorithms can avoid worst-case scenarios.
- For example, using random heights like in skip lists can prevent performance from being biased toward one side.
- However, randomization isn't always good and should be used appropriately depending on the nature of the problem.

<br/>

## 🔖 16.8 Conclusion

- **Data structures have as much impact on program performance and complexity as programming languages or algorithms.**
- Therefore, it's important to understand the internal working principles of each data structure and
  grasp in which situations they are most effective.
- Ultimately, good programs **start with choosing and applying the right data structure for the problem.**
