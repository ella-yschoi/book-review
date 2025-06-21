# Five Lines of Code - 8. Restraining Comments

> Topic: Understanding the dangers of comments, distinguishing and handling various types of comments

<br/>

## 🔖 8.4 Replacing Comments with Method Names

### Book Content

People tend to dislike long method names. However, this applies only to frequently called methods. Languages have the property that the most commonly used words tend to be the shortest. The same applies to codebases. This is perhaps natural because things that are always used need less explanation.

### My Thoughts

I really agreed with the above statement. For example, in JavaScript, the method to check array length is `.length` instead of `checkArrayHowLong`, making it intuitive and good for typing.

And I looked back at my code so far. I wondered if I had included unnecessary explanations in methods that I call frequently. To make communication easy to understand, it's good to put a lot of thought into using concise yet intuitive words. Therefore, from now on, I should maintain frequently used methods in a more concise and accessible form.

<br/>

## 🔖 8.5 Maintaining Comments That Document Invariants

### Book Content

The last type of comment is comments that document non-local invariants. As mentioned several times, this is where bugs are likely to occur. The way to judge this is to ask yourself, 'Can this comment prevent someone from creating a bug?' Below, you can see the suspicious `session.logout` and a comment explaining the reason for that code. Authentication (or similar complex interactions) can be very difficult to test or simulate, so comments are very natural.

```typescript
// Logout to force re-authentication on the next request
session.logout();
```

### My Thoughts

The above comment provides additional information that this logout function call not only logs out the user but also 'forces re-authentication on the next request'. This could be important especially in security-related logic. If this comment didn't exist, other developers might understand the logout function call as simply ending the session and overlook the important part of forcing re-authentication.

Also, comments like the above would help test whether the code works as intended. Complex interactions or security-related features can be difficult to test, so comments would provide the information needed to accurately understand the behavior of those features and write test cases.
