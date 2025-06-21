# Five Lines of Code - 2. Deep Dive into Refactoring

> Topic: Meaning transmission through readability, localizing invariants, making changes possible through addition

<br/>

## 🔖 2.1 Readability and Maintainability

### Book Content

Readability - The property of code for conveying intent, and saying code is readable means it's very easy to understand what the code does.<br/>

Maintainability - An expression indicating how many candidates need to be investigated. Taking a long time in the investigation phase is a sign of poor maintainability. If problems occur in unrelated places when something is modified, it's called fragile. The source of fragility is generally 'global state'. Here, global means something outside the scope we considered.

### My Thoughts

Looking back, I think I spent quite a lot of time debugging due to global settings. For example, I had set a specific style globally but forgot about it and kept looking into why local settings weren't being applied. Or there were times when local settings weren't applied due to library configurations.. Having to investigate many candidates (debugging) means my code at that time was fragile, right..?

<br/>

## 🔖 2.1 Invariants

### Book Content

Invariants - When data is global, someone can read or modify it through other variables connected to the data, potentially corrupting the data by mistake. At this time, properties that don't explicitly check conditions in code are called invariants. (e.g. This number can never be negative.) But it's almost impossible to keep invariants in a valid state. In such cases, refactoring to move them closer together so invariants can be seen more easily is called 'localizing invariants'.

### My Thoughts

Why is it difficult to keep invariants in a valid state? If you set variables to not be reassigned, aren't they immutable? Of course, placing them closer together would increase the possibility of being immutable, but I should ask my study group members about this example to see if there's a fundamental way to block changes.

<br/>

## 🔖 2.2 Use Composition Over Inheritance

### Book Content

Use composition over inheritance. Most refactoring patterns and rules are specifically designed to help with object composition. That is, objects have references to other objects internally.

```typescript
interface Bird {
  hasBeak(): boolean;
  canFly(): boolean;
}
class CommonBird implements Bird {
  hasBeak() {
    return true;
  }
  canFly() {
    return true;
  }
}
class penguin extends CommonBird {
  // inheritance
  canFly() {
    return false;
  }
}
```

```typescript
interface Bird {
  hasBeak(): boolean;
  canFly(): boolean;
}
class CommonBird implements Bird {
  hasBeak() {
    return true;
  }
  canFly() {
    return true;
  }
}
class penguin implements Bird {
  private bird = new CommonBird(); // composition
  hasBeak() {
    return bird.hasBeak();
  } // must explicitly pass the call
  canFly() {
    return false;
  }
  canSwim() {
    return false;
  } // what if one more method is added?
}
```

As in the example above, the biggest advantage of composition is that easy changes are possible through addition. Software components should be open for extension and closed for modification. This is called the open-closed principle.

### My Thoughts

When learning JavaScript, you naturally learn the concept of inheritance, so I didn't think this should be a target for refactoring. So at first I wondered why use something other than inheritance? But seeing the example, I understood. If one more method is added, a compilation error occurs because `penguin` doesn't implement the new method `canSwim`. At this time, if using composition, you can simply define `canSwim()` within the class like `hasBeak()`. But conversely, in the inheritance situation, the worker must remember to override `canSwim()`, so it might be difficult to remember these dependencies.<br/>

Thus, using composition over inheritance would allow you to code more freely, like assembling and replacing Lego blocks.
