# Five Lines of Code - 7. Collaborating with Compiler

> Topic: Understanding the strengths and weaknesses of compilers to utilize them in removing invariants and learning about responsibility sharing

<br/>

## 🔖 7.1 Learning About Compilers

### Book Content

#### Compiler's Strength: Definite Assignment Prevents Access to Uninitialized Variables

Another property that compilers verify is their ability to determine whether a variable has been definitely assigned a value before it's used. This check applies particularly when trying to initialize local variables using if statements.

```typescript
let result;

for (let i = 0; i < arr.length; i++) {
  if (arr[i].name === 'John') result = arr[i];
  return result; // Uncaught SyntaxError: Illegal return statement
}
```

In such cases, the best approach is to teach the compiler what we know, that is, that it definitely contains an element named John.

You can inform the compiler using read-only fields where definite assignment analysis is applied. Read-only fields must be initialized when the constructor ends. That is, they must be assigned in the constructor or at declaration time.

You can use this strictness to verify that specific values exist.

### My Thoughts

#### Understanding Read-only Field Initialization in Constructors

If I buy something and put a name tag on it once, then it can never be changed. Similarly, 'read-only fields' also ensure that once a value is set, it cannot be changed. Such fields are mainly set in a special function called the 'constructor'. The constructor is a function that runs when the object is created, handling the object's basic setup.

Setting read-only fields in the constructor means that when the object is first created, it receives necessary information and maintains that information throughout the object's lifecycle. This method helps objects always maintain expected states and makes programs more stable and reliable.

For example, when borrowing books from a library, each book has a unique book number. This number is assigned when the book first enters the library and never changes afterward.

```typescript
class Book {
  readonly bookID: number; // book number is read-only

  constructor(id: number) {
    this.bookID = id; // initialize book number in constructor
  }

  displayID() {
    console.log('The book ID is:', this.bookID);
  }
}

const myBook = new Book(12345);
myBook.displayID(); // The book ID is: 12345

// myBook.bookID = 67890; // Error: book number cannot be changed
```

In this code, the `Book` class has a read-only field called `bookID`. This field represents the book's unique number, is initialized through the constructor when the object is created, and cannot be changed afterward. By setting it this way, the book's unique number can always be maintained consistently and accurately.

#### Using Strictness to Verify Specific Values Exist

In this context, "strictness" means the compiler's function to strictly check whether variables are initialized and prevent errors. Thanks to these compiler features, we can reduce errors and have confidence that specific values will definitely exist and be used.

In the example below, I define a `Person` class. All person objects have a name and ID, and these values must be initialized when the object is created. This prevents these objects from being used in an uninitialized state anywhere in the program.

```typescript
class Person {
  readonly name: string;
  readonly id: number;

  constructor(name: string, id: number) {
    this.name = name; // initialize name in constructor
    this.id = id; // initialize ID in constructor
  }

  displayInfo() {
    console.log(`Name: ${this.name}, ID: ${this.id}`);
  }
}

const john = new Person('John Choi', 12345);
john.displayInfo(); // Output: "Name: John Choi, ID: 12345"
```

Here, the `name` and `id` fields are declared as `readonly`, meaning these values cannot be changed once set. The compiler ensures that these fields must be initialized in the constructor, which helps maintain all instances of the class in a consistent state.

<br/>

## 🔖 7.1.6 Weakness: Null Dereferencing Damages Applications

### Book Content

The risk of runtime errors requires special attention when dealing with nullable variables. If you don't perform null checks on nullable variables, it's better to assume they are null. It's better to check too much than too little. It's good not to remove null checks unless you're absolutely certain.

### Reference Knowledge: Null Check Methods in TypeScript

Null checking in TypeScript refers to the process of verifying that variables or objects are not null. This is an essential step to ensure program stability and can prevent runtime errors caused by null references. TypeScript provides type checking capabilities that can distinguish between null and undefined, so using this appropriately can increase code stability.

#### 1. Direct Comparison

The most intuitive method is to directly compare variables with null.

```typescript
let value: string | null = fetchStringValue();

if (value !== null) {
  console.log(value); // value is definitely not null at this point
}
```

#### 2. Optional Chaining (`?.`)

Using Optional Chaining introduced in ES2020, when null or undefined, it safely handles without attempting to access methods or properties.

```typescript
let user = {
  name: 'John',
  address: null,
};

let city = user.address?.city; // if address is null, city access is not attempted and undefined is returned
```

#### 3. Non-null Assertion Operator (`!`)

An operator that explicitly tells TypeScript that the value is absolutely not null or undefined. Use with caution and only when you're really sure it's not null.

```typescript
let value: string | null = fetchStringValue();
console.log(value!); // developer must be confident that value is not null
```

#### 4. Truthiness Check

You can simply use if statements to check the truthiness of values, which evaluates null and undefined as falsy values.

```typescript
let value: string | null = fetchStringValue();
if (value) {
  console.log(value); // value is not null or undefined and is not an empty string
}
```

#### Precautions

When doing null checks, you should choose appropriate methods according to context. For example, if values can be 0 or empty strings, truthiness checks may be inappropriate as they can consider these valid values as false. In such situations, direct null comparison may be more appropriate.

<br/>

## 🔖 7.1.10 Weakness: Unintended Behavior Due to Deadlocks and Race Conditions

### Book Content

The category of problems originates from multithreading. Race conditions, deadlocks, starvation, etc. - when there are multiple threads sharing mutable data, a flood of problems can occur.

### Reference Knowledge

#### Race Condition

Race Condition is a situation that occurs when two or more processes or threads simultaneously access and attempt to modify data or system state. At this time, the final operation result can vary depending on execution order, making prediction difficult and consistent results hard to guarantee. Such conditions are common problems in concurrent programming and can damage data integrity, so they must be managed through synchronization techniques.

For example, when two threads try to update the balance of the same bank account simultaneously, one thread's changes can be overwritten by another thread, resulting in incorrect balance calculations. Here, TypeScript follows a single-thread model, so traditional race conditions that occur in multithreading environments don't occur. However, JavaScript and TypeScript handle concurrency through asynchronous programming, so other forms of race conditions can occur.

#### promise.race()

The `promise.race()` method returns a Promise object. This promise object fulfills or rejects with the result value of the first completed promise among the promises in the iterable.

That is, this method creates a race condition, allowing progression to the next task as soon as any of several asynchronous tasks completes. Note that it plays a role in programmatically creating behavior similar to race conditions and is suitable when you only need data from the fastest responding source.

```typescript
let promise1 = new Promise((resolve, reject) =>
  setTimeout(resolve, 500, 'one')
);
let promise2 = new Promise((resolve, reject) =>
  setTimeout(resolve, 100, 'two')
);

Promise.race([promise1, promise2]).then((value) => {
  console.log(value); // "two" - promise2 completes faster
});
```

#### Concurrent Rendering

React Concurrent Rendering was updated in React 18 and is a feature to make React applications' rendering engine more efficient. It aims to handle multiple asynchronous tasks simultaneously, improve performance and rendering engine, and enhance user experience. Through this mode, React can prioritize rendering tasks, process more important updates first, and interrupt or delay less important tasks.

<br/>

## 🔖 7.2 Using the Compiler

### Book Content

Programming is not architecture but multiple stages of communication. Therefore, programming has much more in common with literature. You acquire knowledge about the domain, form a model in your mind, then codify this model into code.

Dan North noticed the similarity that a program is the development team's shared knowledge about a domain frozen in time. 'A program is a complete and unambiguous description of everything the developer believes to be true about the domain.' In this analogy, the compiler is an editor who checks whether our text meets certain quality standards.

### My Thoughts

Let me understand Dan North's analogy. When developing programs, developers write code based on understanding of specific fields (domains). Here, "domain" refers to the specific subject or field where the software being developed will be applied. For example, if developing banking software, that domain would be financial services.

What developers "believe to be true about the banking domain" would include their understanding of how rules, principles, data, etc. that they recognize as facts in the banking field - for example, processes like account transfers, interest calculations, loan approvals in the financial domain - should be carried out.

"Complete and unambiguous description" means accurately expressing this domain knowledge in program code. That is, the developer's (believed to be true) domain knowledge must be accurately and clearly reflected through code. (Machines don't lie..) Code should have no ambiguity or uncertainty, and the program should accurately follow and implement the domain's facts and rules.

In the above analogy, the compiler plays the role of an "editor". I thought it was a very accurate analogy. Just as an editor reviews whether writing is clear and accurate before publishing a book or document, the compiler reviews whether code is syntactically correct, executable, and correctly reflects the domain's rules. The compiler is a very good friend who checks that code has no errors and meets given specifications, helping the program work as the developer intended.

<br/>

## 🔖 7.2.2 Don't Fight the Compiler

### Book Content

The type checker is the most powerful part of the compiler. People disable the type checker by using types incorrectly in various ways.

The first is using 'type casting'. Type casting is like telling the compiler that you know better than the compiler. Type casting prevents the compiler from helping you and essentially disables it for specific variables or expressions.

### My Thoughts

#### Understanding Type Casting

Type casting or Type Casting in programming refers to the act of explicitly converting one variable type to another type. Through this, programs can make various types of data compatible or enable specific operations. However, if type casting is used incorrectly in this process, it bypasses the compiler's type checking function, increasing the risk of runtime errors.

Type casting is basically like telling the compiler, "I know this variable is actually a different type, so it's safe to perform this operation." This way, the compiler trusts the information provided by the developer and skips the verification procedure for that type. However, since it might actually be an unsafe situation, type casting should be used carefully.

#### Example of Compile Error Due to Type Casting

Below is an example of incorrect use of type casting commonly seen in JavaScript (including TypeScript). JavaScript is a dynamically typed language, but TypeScript has a strong type system, so misuse of types through type casting can cause problems.

```typescript
function calculateLength(vector: { x: number; y: number }) {
  return Math.sqrt(vector.x ** 2 + vector.y ** 2);
}

const point = { x: 1, y: 2, z: 3 };

// Error: Object literal may only specify known properties, and 'z' does not exist in type '{ x: number; y: number; }'
console.log(calculateLength(point as { x: number; y: number }));
```

In the code above, the `calculateLength` function takes an object with `x` and `y` properties as an argument and calculates the length of the vector. However, the `point` object includes the `z` property as well. In TypeScript, if you pass the `point` object directly to the function, a compile error occurs due to type mismatch. Therefore, `(point as { x: number; y: number })` is used to attempt type casting, which disables the compiler's type checking. In this case, the compiler determines that it's okay to ignore the `z` property, but if the actual code logic requires `z`, it can lead to runtime errors.

Therefore, when using type casting, it's important to thoroughly review whether the data is actually suitable for the type you're trying to convert to, and you should write code safely with the help of the type system as much as possible.

<br/>

## 📚 References

- Book <Learning TypeScript>
- [TypeScript Deep Dive - Readonly](https://radlohead.gitbook.io/typescript-deep-dive/type-system/readonly)
- [TypeScript Tutorial - Type Casting](https://www.typescripttutorial.net/typescript-tutorial/type-casting/)
- [promise.race()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)
- [Problems with JavaScript Callbacks and Why Use Promises](https://yuddomack.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%BD%9C%EB%B0%B1%EC%9D%98-%EB%AC%B8%EC%A0%9C%EC%A0%90%EA%B3%BC-%ED%94%84%EB%A1%9C%EB%AF%B8%EC%8A%A4-%EC%93%B0%EB%8A%94-%EC%9D%B4%EC%9C%A0)
- [React 18's New Features - Concurrent Rendering, Automatic Batching, etc.](https://www.freecodecamp.org/korean/news/riaegteu-18yi-singineung-dongsiseong-rendeoring-concurrent-rendering-jadong-ilgwal-ceori-automatic-batching-deung/)
