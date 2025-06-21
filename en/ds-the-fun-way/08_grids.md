# Data Structures the Fun Way - 8. Grids

> What happens when considering multidimensional values or objects?

<br/>

## 🔖 8.1 Introduction to Nearest Neighbor Search

### What is Nearest Neighbor Search?

- Finding the data point closest to a given search target
- Closely related to target value search in binary search, but the difference is the 'criterion for successful search'
- Nearest neighbor search only needs to find the closest item

### Nearest Neighbor Search Using Linear Scan

- Can be implemented with simple loops in most programming languages
- Easily supports different distance functions or multidimensional points

<br/>

## 🔖 8.2 Grids

### What is a Grid?

- A data structure for storing 2D data, composed of a fixed series of cells like arrays
- Unlike arrays, you cannot limit each box to contain only one value
- Determines each storage location using the coordinates of data points
- Can limit search by utilizing the spatial structure of data

### Grid Structure

- The top-level data structure representing the grid includes additional information for management
- When creating a grid, you can perform a for loop on data points

### Removing Points

- It's difficult to determine which point to remove from a box
- Find the correct box, traverse the linked list to find the same point, and remove it from the list

<br/>

## 🔖 8.3 Searching in Grids

### Box Pruning

- Check whether there's at least one point within a given distance from the target point in any box
- By considering each dimension independently, you can calculate the distance between any point and the boundary of a grid box

### Expansion Search

- Expand the search range from the box containing the target point and continue searching until finding the nearest neighbor
- Ideal expansion search method for boxes
