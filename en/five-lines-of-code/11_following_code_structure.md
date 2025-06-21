# Five Lines of Code - 11. Following Code Structure

> Topic: Encoding behavior (actions) in control flow and identifying unused structures

<br/>

## 🔖 11.1 Structural Classification by Scope and Origin

### Book Content

User behavior also affects code structure. Some code structure changes require changes in user behavior. Users can be seen as another part of the code. If you cannot interact with users, users are external factors. Therefore, from a refactoring perspective, you need to care about users, which constrains refactoring.

(omitted) Therefore, it's useful to first model user behavior as-is, including all inefficiencies, then gradually provide more efficient functionality through training and education to refactor user behavior.

### My Thoughts

In the above content, the perspective and word choice of 'refactoring user behavior' was interesting. This is because I thought refactoring was a word that only applied to code. Since it was an unfamiliar perspective, I thought about what cases might exist.

Cases where user behavior affects code structure are probably frequent when changes related to UI design and interaction are needed? When users don't behave as developers expect, existing code might need to be modified or new features added.

For example, on a web page, users need to enter text in a comment input field and click the "Add Comment" button to leave a comment. But users want to quickly enter comments without using a mouse. At this time, developers would probably modify existing code based on user feedback to make the comment input feature more convenient.

### Others' Thoughts

I interpreted the 'user' mentioned in the book as an end user, but some study group members interpreted it as a 'developer'. That is, from the perspective of users who handle code.

The code modification for feature improvement that I gave as an example above is closer to service development rather than from a refactoring perspective. Generally, refactoring maintains the functionality that end users experience while only changing code or structure. Rather, examples of developers refactoring their behavior for specific libraries or frameworks seem more relatable.

Listening to others, their perspective seems closer to what the book is saying.

<br/>

## 🔖 11.2 Three Ways to Code Behavior

### Book Content

Regardless of where behavior originates, there are three ways to reflect behavior in code.

- Control Flow: Simply expressing behavior as text through enumerated lines of code (e.g. control operators, method calls, etc.)
- Data Structure: Structure for organizing and storing data, increasing performance optimization and efficiency of data management (e.g. binary search trees, hashes, stacks, queues, etc.)
- Data Itself: Analyzing data to find patterns within it and creating data structures based on this (e.g. ML, etc.)

### My Thoughts

Chapter 11 mainly explains methods for coding behavior. That is, it explains different approaches to writing and structuring code.

This seems to align with the essence of software. Many people say that software is about automating complex processes in the real world for users or solving difficult business problems. Therefore, to implement complex and difficult business domains in software, you need to correctly understand that domain. Additionally, this book also says that relationships in the real world should be expressed in code, and code is a codified structure from the real world.

<br/>

## 🔖 11.6 Utilizing Unused Structures

### Book Content

Generally, basic domains tend to be older than software, so they are more mature and experience less dramatic changes. Therefore, structures from domains can often be safely utilized.

### My Thoughts

Why can structures from domains be safely utilized? I thought about several reasons.

Most domains (excluding new technologies like finance, commerce, etc.) have developed over a long time, and in that process, specific rules and practices have been well established. Implementing code based on such verified rules and practices can reduce risks.

Additionally, experts in each domain have deep understanding needed to solve complex problems in their field. By reflecting this expert knowledge in software design, more accurate and efficient systems can be built. (e.g. exception handling needed in financial domain, interest calculation methods, etc.)

<br/>

## 📚 References

- [What is Domain-Driven Design](https://engineering-skcc.github.io/msa/DDD-WhatIsDdd/)
