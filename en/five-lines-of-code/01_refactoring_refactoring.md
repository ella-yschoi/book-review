# Five Lines of Code - 1. Refactoring Refactoring

> Topic: Components of refactoring, introduction to refactoring and the need for safety

<br/>

## 🔖 1.0 Components of Refactoring

### Book Content

In software development, problems indicate that skills, culture, and tools are insufficient or their combination is lacking. Therefore, refactoring requires all components.

1. Skills - Skills to identify what code is wrong and needs refactoring.
2. Culture - Culture and procedures that encourage spending time on refactoring.
3. Tools - Something to ensure that the work we do is safe.

### My Thoughts

Since I'm mostly doing projects alone now, I thought refactoring was simply a process for writing better code. But looking at each component explained in the book, it seems right. Especially in the workplace, the 'culture' part seems very important. If an organization only values meeting deadlines by implementing features and rarely does refactoring, it would be difficult to do deep, thoughtful coding.<br/>

Personally, I value considering others' perspectives when documenting and enjoy developing this skill. So I also enjoy the refactoring process in development and feel great achievement when positive feedback comes back. I'm looking forward to learning more systematic refactoring methods from this book.

<br/>

## 🔖 1.1 What is Refactoring?

### Book Content

Refactoring means changing code without changing functionality. And there are several reasons why refactoring should be done.

- To make code faster
- To make code smaller
- To make code more general or reusable
- To improve code readability and make maintenance easier

Therefore, the definition of good code is 'code that is easy for people to read, easy to maintain, and works as intended.'

### My Thoughts

This seems to be the core sentence of this book. But do we need to satisfy all four conditions above? Can't we say it's (somewhat..) good code even if we satisfy just one of the four conditions? Or satisfying one might naturally lead to satisfying another. For example, reusable code makes maintenance easier.

<br/>

## 🔖 1.3 When to Refactor?

### Book Content

_'Make it easy to change first, then change' - Kent Beck_<br/>
Every time we implement something new, we refactor first to make it easy to add new code. This is similar to preparing necessary ingredients before starting to cook.

### My Thoughts

Usually when coding, I start by putting everything in places like `app.tsx`, then separate components into smaller units for separation of concerns. So I agreed with this statement. The reason I think a lot about how to separate when refactoring is because I think from a long-term perspective: how can I make it easy to add new code? Actually, it's also because I'm lazy to do the work twice later..
