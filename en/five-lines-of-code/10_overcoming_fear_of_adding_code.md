# Five Lines of Code - 10. Overcoming Fear of Adding Code

> Topic: Overcoming fear of adding code

<br/>

## 🔖 10.5 Impact of Copy and Paste on Speed

### Book Content

Code duplication has two important characteristics to consider. First, when code is shared, it easily affects all locations where the code is used. This means you can quickly make global changes to behavior. However, changing behavior for only one part of the calling side is not simple. On the other hand, when code is copied to share behavior, each call is separated, making it easy to change according to the calling side.

Sharing code increases the speed of global behavior changes, and copying code increases the speed of local behavior changes.

### My Thoughts: What's the difference between code sharing and code duplication?

#### Example of Code Sharing

Code sharing means using one code block in multiple locations. This follows the DRY (Don't Repeat Yourself) principle, maximizing code reuse to facilitate maintenance. However, when shared code is changed, it inevitably affects everywhere that code is used.

```javascript
function updateInventory(item) {
  // inventory update logic
  console.log(`Updating inventory for ${item}`);
}

// calling the function in various places
updateInventory('Apples'); // Updating inventory for Apples
updateInventory('Oranges'); // Updating inventory for Oranges
```

In this example, the `updateInventory` function is used to update inventory for two products: "Apples" and "Oranges". When the function is modified, the inventory update method for all products changes.

#### Example of Code Duplication

Code duplication is copying a specific code block to use in different contexts. This **allows each copy to be modified independently**, making it **easy to change to fit specific use cases**. However, this method increases code duplication, making maintenance difficult.

```javascript
function updateInventoryApples() {
  // inventory update logic for "Apples"
  console.log('Updating inventory for Apples');
}

function updateInventoryOranges() {
  // inventory update logic for "Oranges"
  console.log('Updating inventory for Oranges');
}

// each function is called independently
updateInventoryApples(); // Updating inventory for Apples
updateInventoryOranges(); // Updating inventory for Oranges
```

In this case, separate inventory update functions exist for "Apples" and "Oranges", so changing one doesn't affect the other. However, since code is duplicated, you might need to repeatedly apply similar changes to multiple functions.

#### Conclusion

Code sharing simplifies maintenance but affects all related parts when global changes are needed. On the other hand, code duplication facilitates local changes but makes maintenance complex. Therefore, when designing, you should carefully consider the balance between project requirements and ease of maintenance when choosing which method to use.

<br/>

## 🔖 10.6 Change by Addition Through Extensibility

### Book Content

Another way to add code is to use extensibility. If you know that some code must be receptive to change, you can make it extensible.

Points where variations occur make code more complex. It can be more difficult to modify later because understanding the code flow is more difficult. Therefore, making everything extensible is wasteful as it makes code unnecessarily complex.

Complexity unrelated to the domain is called 'accidental complexity'. Since code represents the real world, some complexity is inherited from the domain. This is called 'essential complexity'.

### My Thoughts

#### Understanding Accidental Complexity

Accidental complexity is unnecessary additional complexity that occurs during software development. It's also called incidental complexity. This can arise from design decisions, technology choices, or inefficiencies in the development process, and is complexity that's not inherently necessary. It's good to remove or minimize this.

For example, when writing different database call code for each database to ensure compatibility with various databases, this can increase accidental complexity. This can be reduced by abstracting it using interfaces or patterns.

#### Understanding Essential Complexity

Essential complexity refers to the complexity of the problem itself. This is the fundamental complexity of the domain (field), the complexity naturally possessed by real-world problems that the software aims to solve. This complexity is unavoidable and must inevitably be reflected in the software.

For example, code that processes transactions in a banking system must include various validations and exception handling. This is to ensure legal requirements and safety of financial transactions, well demonstrating essential complexity.

<br/>

## 📚 References

- [Complexity and OOP](https://redutan.github.io/2016/10/13/complexity-and-oop)
