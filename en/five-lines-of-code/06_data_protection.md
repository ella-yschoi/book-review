# Five Lines of Code - 6. Data Protection

> Topic: Using encapsulation to restrict access to data and functionality, making invariants affect only local areas

<br/>

## 🔖 6.1 Encapsulation Without Getters

### Book Content

#### (1) Don't Use Getters and Setters

Classes combine functionality for the same data to localize invariants more closely. Don't use getters or setters for fields that are not boolean.

The moment a getter exists for an object's field, it breaks encapsulation and makes invariants global. After returning an object, the place that receives it can pass this object to many more places, which we cannot control. Any place that gets the object can call public methods and modify the object in unexpected ways.

#### (2) Encourage Push-Based Architecture

In pull-based architecture, data is fetched and operations are performed centrally, leading to numerous 'passive' data classes and a few 'manager' classes that mix data from here and there to perform all operations. This approach creates tight coupling between data and managers, and implicitly between data classes as well.

```typescript
class Website {
  constructor(private url: string) {}
  getUrl() {
    return this.url;
  }
}

class User {
  constructor(private username: string) {}
  getUsername() {
    return this.username;
  }
}

class BlogPost {
  constructor(private id: string, private author: User) {}
  getId() {
    return this.id;
  }
  getAuthor() {
    return this.author;
  }
}

function generatePostLink(website: Website, post: BlogPost) {
  let url = website.getUrl();
  let user = post.getAuthor();
  let name = user.getUsername();
  let postId = post.getId();

  return url + name + postId;
}
```

In push-based architecture, operations are moved as close to data as possible, and data is passed as arguments instead of fetching data. As a result, all classes have their own functionality, and code is distributed according to its utility.

```typescript
class Website {
  constructor(private url: string) {}
  generateLink(name: string, id: string) {
    return this.url + name + id;
  }
}

class User {
  constructor(private username: string) {}
  generateLink(website: Website, id: string) {
    return website.generateLink(this.username, id);
  }
}

class BlogPost {
  constructor(private id: string, private author: User) {}
  generateLink(website: Website) {
    return this.author.generateLink(website, this.id);
  }
}

function generatePostLink(website: Website, post: BlogPost) {
  return post.generateLink(website);
}
```

### My Thoughts

#### (1) What are the benefits of inlining methods?

First, 'inlining' simply means replacing function or method calls with the code that corresponds to that call. That is, it can be said to simplify code by replacing function calls with the code contained in that function.

```typescript
// Original code
function add(a: number, b: number): number {
  return a + b;
}

function calculateSum(x: number, y: number): number {
  return add(x, y);
}

// Inlined code
function calculateSum(x: number, y: number): number {
  return x + y; // Replace add function call with addition operation, eliminating function call
}
```

Getting back to the main point, in the push-based example code above, `generatePostLink` is likely to be inlined because it's just one line without additional information. In this case, `generatePostLink` only calls the `generateLink` method. This way, you can understand the arguments passed through function calls more directly and convey the intent of the code more clearly.

Therefore, through such inlining, you can remove the overhead of function calls and simplify code to make it more intuitive, ultimately making maintenance easier. However, excessive inlining can reduce code readability, so it should be used in appropriate situations.

#### (2) Why shouldn't we use getters or setters for fields that are not boolean?

Boolean fields are mainly used to represent 'object state', it was said. For example, let's say there's `isActive` representing an activated state and `isSwitchedOn` representing a switch state. In such cases, it would generally be okay to use getters that return boolean values and setters that set boolean values.

```typescript
class Example {
  private isActive: boolean;

  constructor(isActive: boolean) {
    this.isActive = isActive;
  }

  public getIsActive(): boolean {
    // getter
    return this.isActive;
  }

  public setIsActive(active: boolean): void {
    // setter
    this.isActive = active;
  }
}
```

In the code above, the reason for setting the `isActive` field to private is to prevent direct access or modification of this field from outside the class.

On the other hand, the reason for setting the `getIsActive` method to public is so that users of the class can read the state of the isActive field. If the `getIsActive` method were also set to private, code outside the class would have no way of knowing the value of this field, making it impossible to utilize the information this field holds. → That is, **methods that return boolean values should be public!**

However, other types of fields, especially those that directly expose an object's internal state, are generally not good. This is because it violates the encapsulation principle and can increase dependency on the object's internal implementation. Getters and setters are mainly used to restrict access to fields and to read or modify those fields when necessary.

For example, let's assume there's an `age` field representing a user's age as shown below.

```typescript
class User {
  private age: number;

  constructor(age: number) {
    this.age = age;
  }

  public getAge(): number {
    return this.age;
  }

  public setAge(age: number): void {
    this.age = age;
  }
}
```

In the example code above, there are getters and setters for the `age` field. This way, the age value can be directly read and modified from outside, exposing the object's internal implementation. **Instead, it would be more desirable to provide methods that change the object's state to maintain encapsulation of the object's internal state.**

#### (3) What is tight coupling?

Tight coupling represents **a state where software components are strongly dependent on each other**. This means that one component knows too much about another component's internal implementation, or that changes in other components affect that component. Tight coupling that often appears in object-oriented programming mainly appears in the following forms:

- **Direct Dependency**: Occurs when one class **directly calls** another class's methods or **directly creates** instances of another class. This increases dependency between classes and can create code vulnerable to changes.
- **Ignoring Interfaces**: Occurs when a class **directly accesses** another class's internal state or depends on another class's internal implementation. This reduces flexibility and makes code reuse difficult.
- **Strong Coupling**: Occurs when two classes modify each other's internal state or send messages directly to each other. This increases the ripple effects of changes and can make understanding and maintaining code difficult.

Therefore, using getters and setters can often lead to tight coupling. Directly accessing and modifying a class's internal state from outside breaks the class's encapsulation and can increase dependencies.

#### (4) Let's understand the following content better

> (The story about not using getters or setters for fields that are not boolean, the story about tight coupling problems occurring..) These problems only occur with mutable objects. However, this rule applies private fields to immutable fields as well, making only Boolean an exception due to the proposed architecture.

I read this part several times because I didn't understand it, but let me organize it in my own words.

[Summary]<br/>
**In conclusion, immutable objects generally avoid directly exposing internal state, but for Boolean fields, it's allowed to use getters and setters to expose them externally.**

First, let me go through each term. The "coupling" mentioned in the book represents mutual dependency between objects. If coupling is strong, changes in one object can have a big impact on other objects. To avoid such situations, it's desirable not to couple partially changing objects with other objects.

And "mutable objects" means objects whose state can be changed. On the other hand, "immutable objects" means objects whose state doesn't change after creation.

In the explanation above, saying "Boolean is an exception" means that while immutable objects generally avoid directly exposing internal state to the outside, Boolean fields are allowed as an exception. What's the reason?

Boolean fields are often used as "flags representing the current state of an object", so there are often times when it's necessary for the outside to check this state. Such fields should mainly be accessible and modifiable from outside using getters and setters. For example, there might be logic where the outside needs to determine whether some object is in an 'active state' or not. In such cases, it would be appropriate to set the `getIsActive` method that can read state information to `public`.

Additionally, Boolean fields are usually not changed much, and even when they are changed, the impact often doesn't significantly affect other objects. Therefore, it's allowed to use getters and setters to expose such Boolean fields externally, and it seems appropriate to make such fields private to reduce coupling.

<br/>

## 🔖 6.2 Encapsulating Simple Data

### Book Content

Code should not have methods or variables with common prefixes or suffixes. If multiple elements have the same affix, it indicates the cohesiveness of those elements, but a better way is to use 'classes'.

The advantage of grouping such methods and variables using classes is that you can completely control the external interface. You can hide helper methods to avoid polluting the global scope.

```typescript
class Account {
  // deposit() private, cannot call directly
  private deposit(to: string, amount: number) {
    let accountId = database.find(to);
    database.updateOne(accountId, { $inc: { balance: amount } });
  }

  // Instead, deposit() is called through transfer()
  transfer(amount: number, from: string, to: string) {
    this.deposit(from, -amount);
    this.deposit(to, amount);
  }
}
```

In the example code above, operations related to the deposit method are encapsulated within the Account class to control external access. This allows complete control of the external interface and hiding helper methods to avoid polluting the global scope.

The smell derived from this rule is called the 'Single Responsibility Principle', and **a class should have only one responsibility.** The structure implied by common affixes means that those methods and variables share the responsibility of the common affix. Therefore, such methods should have a separate class dedicated to this common responsibility.

### My Thoughts

#### (1) Modifying classes that follow and don't follow the Single Responsibility Principle

Around the time of the final mission of Woowacourse, I had a lot of concerns about how to take responsibility for each class in the Model among MVC. I separated responsibilities by drawing branches that each class should be responsible for in the main logic, trying to ensure that when changes occur in one class, they don't affect other classes.

For example, there was a class that assigned badges according to the following conditions.

```javascript
import { BADGES, OUTPUT } from '../common/constants.js';

class Badge {
  #totalBenefit;

  constructor(totalBenefit) {
    this.#totalBenefit = totalBenefit;
  }

  assignBadge() {
    for (const { badge, amount } of BADGES) {
      if (this.#totalBenefit >= amount) {
        return badge;
      }
    }
    return OUTPUT.none;
  }
}

export default Badge;
```

This class has one clear responsibility: `assignBadge()`, that is, assigning badges. It also uses externally defined `BADGES` and `OUTPUT` constants, understanding this class's external dependencies and separating them into other responsibilities.

On the other hand, looking at the date-related code below, it doesn't seem to follow the Single Responsibility Principle well.

```javascript
import { ERROR, EVENT } from '../common/constants.js';
import { isInRange, isNumeric } from '../common/validator.js';
import { throwError } from '../common/utils.js';

class EventDate {
  #date;

  constructor(date) {
    this.#validate(date);
    this.#date = Number(date);
  }

  getEventDate() {
    return this.#date;
  }

  #validate(date) {
    this.#validateNumber(date);
    this.#validateRange(date);
  }

  #validateNumber(date) {
    if (!isNumeric(date)) {
      throwError(ERROR.date);
    }
  }

  #validateRange(date) {
    if (!isInRange(date, EVENT.min_date, EVENT.max_date)) {
      throwError(ERROR.date);
    }
  }
}

export default EventDate;
```

In the class above, the `#validate` method checks whether it's a number and the range, and the constructor converts the given date to a number and stores it.

If I were to rewrite the above code, I would separate the utility class or function responsible for validation and conversion to change it into a class that follows the Single Responsibility Principle. For example, I would design the `EventDate` class to only perform the role of storing and providing dates, and separate validation and conversion into separate utility functions or classes.

```javascript
import { ERROR, EVENT } from '../common/constants.js';
import { isInRange, isNumeric } from '../common/validator.js';
import { throwError } from '../common/utils.js';

// Separate into utility class responsible for date validation and conversion
class DateValidator {
  static validate(date) {
    if (!this.isNumeric(date) || !this.isInRange(date)) {
      throwError(ERROR.date);
    }
  }

  static isNumeric(date) {
    return !isNaN(date);
  }

  static isInRange(date) {
    return date >= EVENT.min_date && date <= EVENT.max_date;
  }
}

// Class that performs only one role of storing and providing dates
class EventDate {
  #date;

  constructor(date) {
    DateValidator.validate(date);
    this.#date = Number(date);
  }

  getEventDate() {
    return this.#date;
  }
}

export default EventDate;
```

#### (2) So should a class have only one method?

The Single Responsibility Principle means that a class performs one purpose, not that a class must have only one method.

Actually, classes can have multiple methods. But these methods should all handle various aspects of the class's main purpose, and ultimately all should be parts to achieve the class's main purpose.

For example, a Car class can have multiple methods needed for driving. There can be methods to start and stop driving, methods to control speed, methods to change direction, etc. These multiple methods all handle work related to the car's driving, so such a class can follow the Single Responsibility Principle.

Therefore, the number of methods in a class doesn't matter. What's important is whether the class performs one purpose and appropriately includes the methods necessary to achieve that purpose.

<br/>

## 🔖 6.4 Removing Invariants That Exist in Order

### Book Content

When something must be called before something else, it's called a sequence invariant. This technique can be utilized to always make things happen in a specific order. This refactoring technique is called 'order enforcement'.

The most wonderful type of refactoring is when you can teach the compiler how you want the program to execute. This helps ensure it runs as desired. In object-oriented languages, constructors must always be called before object methods.

Using constructors to make some code execute becomes evidence that the code has been executed for the class instance. This is because you cannot get an instance without successfully executing the constructor.

### My Thoughts

I used an easier example than the book. In the example below, I define a `User` class and initialize the username in the constructor. It also includes functionality to output greetings through the `greet` method. The constructor is called first to set the name, then the method can use that name.

```typescript
class User {
  private name: string; // username

  constructor(name: string) {
    console.log('Constructor is called.');
    this.name = name; // initialize name in constructor
  }

  greet() {
    console.log(`Hello, ${this.name}!`); // method to output greeting
  }
}

// Create object
const user = new User('Ella');

// Call method
user.greet();
```

When this code is executed, it outputs in the following order:

1. "Constructor is called." - Output when the `User` object's constructor is called.
2. "Hello, Ella!" - Output when the `greet` method is called with the username.

Through the example above, you can see that the object's constructor executes before the object's other methods (order enforcement) to perform necessary initialization work. Values set through the constructor will be utilized in methods called afterward.

<br/>

## 🔖 6.5 Another Way to Remove Enums

### Book Content

In many languages including TypeScript, enums cannot have methods. In this case, you can work around it using private constructors. All objects must be created by calling constructors. Making constructors private means objects can only be created within the class. Particularly, you can control the number of existing instances. Putting such instances in public constants allows them to be used as enums.

### My Thoughts

#### (1) Enums in TypeScript cannot have methods?

Enums are features that help use sets of specific values easily and safely. Enums are basically used to define sets of fixed constants containing numeric or string values, and saying that enums cannot have methods means that enums themselves are different from objects that can include methods. Also, they don't support Tree-Shaking.

To summarize, while general classes can define behavior (methods) along with data (properties), enums are used only to list data (specific values), and you cannot directly add methods to this data.

<br/>

## 📚 References

- [Modern JavaScript Tutorial - Property getters and setters](https://ko.javascript.info/property-accessors)
- [[JS] 📚 Complete Guide to JavaScript's Getter & Setter](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-getter-setter-%EB%9E%80)
- [LINE Engineering - Introducing why TypeScript enum should not be used from a Tree-shaking perspective](https://engineering.linecorp.com/ko/blog/typescript-enum-tree-shaking)
