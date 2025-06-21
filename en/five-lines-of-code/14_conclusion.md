# Five Lines of Code - 14. Conclusion

> The book [<Five Lines of Code>](https://www.yes24.com/Product/Goods/116904325) explains a systematic and effective refactoring training method starting with the five-line limit rule. I conducted a [refactoring study](https://github.com/elegant-functional-2023/five-lines-of-code) reading this book with several developers, and below is a summary of what I personally felt. Content summary and personal opinion summary is [here](https://github.com/ella-yschoi/book-review/tree/main/five-lines-of-code).

<br/>

## Developing Good Habits on a Blank Slate

Until now, I had only read development language or framework concept books, but this was my first book about refactoring or clean code. Thanks to this, I was able to modify the code I had written in a better direction. If I can better 'embody' the learned rules and methods, it seems like it could give me good coding habits for me, who is still close to a blank slate.

It's regrettable that I couldn't do much example practice while reading the book due to time constraints. I plan to do step-by-step refactoring even on simple code and record the process on my blog. This book is too precious to just let pass by.

The author says in the preface. Many rules are derived from Robert C. Martin's <Clean Code>, and numerous refactoring patterns originate from Martin Fowler's <Refactoring>. I haven't read these two books yet, but since they're frequently cited in this book, I'm curious and plan to read them later and apply them to code.

<br/>

## Refactoring Without Answers

Recently, I had a coffee chat with a development lead and talked about refactoring, which stuck in my memory so I'm writing it down. For example, what part would be good to refactor in the code below?

```javascript
function sum(a, b) {
  return a + b;
}

sum(2, 3);
```

There might not be anything specific, but if we look hard enough. Type checking, more semantic naming of parameters, null checking, or whether to handle with throw Error, how to handle it if we do, or how to return could also be checked additionally.

Even in simple code like above, there are things to refactor. Or someone might say there's nothing. There's no right answer. Like this, where each person places more value when writing code will also have a big influence.

In a related story, it's also said that development can be compared to novels. Variables are characters, and functions are the roles that person has. How well you explain this determines whether the novel is well-written or not, and ultimately how well the reader understands it. Code is the same. How well you write code is similar to the realm of writing.

<br/>

## Breaking Code Like King Cutlet

I remembered advice my team leader gave me when I was struggling with too much work at my first company. 'Eating king cutlet in pieces'. When king cutlet first comes out, it looks too big, but if you cut it into small pieces and eat it slowly, you can finish it all. Work is the same, so don't worry too much and try to break down work according to priorities.

As mentioned in the book's conclusion chapter, the ability to decompose big problems into small pieces is said to be an important part of programming. Ultimately, to solve problems, you need to understand the problem well (e.g., massive legacy code refactoring or migration, etc.) and break the problem into small units to find the cause. Then, if you work on it little by little according to solutions corresponding to each problem, the problem that only looked big will be solved before you know it.

<br/>

## Rules Are Not the Answer

At the end of the book, the author pleads once more. If the rules mentioned so far help provide stability and confidence when refactoring, nothing could be better. Don't use rules as tools to evaluate each other. The rules in this book will be a 'good starting point' for sharing opinions about code quality.

This really resonated with me. When doing code reviews, there are sometimes people who say this method is correct because they mentioned some rule in a book (supposedly). Of course, the principles and methods mentioned in the book probably aren't wrong. But just because the book says it doesn't mean everything is correct, and it might not be the right method in our situation or context.

So, as the author says, rather than blindly following the rules mentioned here, what if we think of them as guidelines that can guide our code and service in a better direction?
