# Data Structures the Fun Way - 9. Spatial Trees

> Further improving nearest neighbor search based on tree-based data structures and the concept of spatial partitioning

<br/>

## 🔖 9.1 Quad Trees

### Limitations of Grids

- Using many grid boxes requires considerable memory and searching many boxes
- If grids are divided coarsely (?), many points enter boxes, resulting in similar outcomes to simple linear scans
- For this, it's good to dynamically partition space according to data -> uniform quad trees

### Uniform Quad Trees

- Introduces the branching structure of trees into grids, where each node of the tree represents each region of space
- Creating quad trees like art by recursively partitioning allocated space into smaller and smaller sub-regions
- Adding points to nodes is done by traversing the tree to find the position of new points.

### Removing Points

- Similar to inserting points but follows a more complex process.
- Like grids, you can add arbitrarily close points or insert duplicate points.
- Wrapper functions check if points are within tree boundaries, then call recursive removal functions

### Searching in Uniform Quad Trees

- In uniform quad trees, search starts from the root node.
- And first check if points closer than the current candidate can be included.
- Like the example in the book, it's like asking a lazy neighbor where to throw the ball to get it over to our yard in the shortest distance.

<br/>

## 🔖 k-d Trees

### Structure of k-d Trees

- k-d trees are built based on concepts similar to quad trees.
- It's a spatial data structure that combines the spatial partitioning of quad trees with the characteristic of binary search trees branching into two children, taking advantage of both data structures.
- k-d trees select one dimension and partition data based on that dimension.
- Due to their flexible structure, more care is needed when handling spatial boundaries of nodes.
- Due to complexity, each k-d tree node stores a considerable amount of information.

### Strict Spatial Boundaries

- Using strict boundaries allows k-d trees to be better applied to data.
- Since the regions obtained through strict boundaries are much smaller, pruning can be done more powerfully.
- Therefore, the likelihood that the minimum distance between the target point and boundary becomes larger increases.

### Creating k-d Trees

- Goes through a recursive process similar to binary search tree creation but with differences.
- Initially starts with all data points, selects the dimension and value to partition, and divides data points into two subsets.
- To avoid infinite recursion when there are duplicate data, use one of the last two tests.
- The main difference is that k-d trees select only one dimension to partition at each level.

### Operations of k-d Trees

- Basic operations are insertion, removal, and search of points, using the same methods as quad trees.
- All operations start from the top and move to appropriate branches using partition values.
- The main difference is that instead of choosing which quadrant to search, you choose one of two children.

<br/>

## 🔖 9.3 Why Quad Trees and k-d Trees are Important

### Key Summary

- Using quad trees allows adjusting grid resolution according to the density of data points in local regions by partitioning multiple dimensions at once.
- k-d trees are concepts that borrow the core idea of binary trees to solve nearest neighbor search problems.
- By using the method of selecting one dimension to partition at each node, it solves the problem of branching factor becoming large in trees representing multidimensional data.
- Also, k-d trees can respond more flexibly to data structures as they adapt to the data structure and provide more pruning.
