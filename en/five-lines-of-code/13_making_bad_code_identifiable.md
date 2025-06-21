# Five Lines of Code - 13. Making Bad Code Identifiable

> Topic: Why good code and bad code should be separated, rules for marking code as bad

<br/>

## 🔖 13.1 Attitude Toward Bad Code

### Book Content

This chapter discusses methods to make bad code look obviously bad to clearly indicate quality levels. This is called 'anti-refactoring'.

Leaving bad code has two advantages. It's easy to find again and gives a signal that control is not sustainable. To convey bad code to alert about problems, considerable psychological stability is needed. We must have the belief that we won't be blamed.

(omitted) If you can't make it well, you must make it noticeable. I think bad code is better than not-bad code. If you don't have the time or skills to exceed the standard of 'very good', you should just leave it 'bad'. This activity separates code into clean code and legacy code.

If you can tell at a glance whether code is clean code or legacy code, you can easily estimate the file ratio between good code and bad code. This information can be used to guide where to put refactoring efforts.

### My Thoughts 1: Counterargument to Author's Opinion

This is somewhat provocative content. There might be parts that don't align with reality or room for debate. In the workplace, teams with an atmosphere where you can comfortably submit PRs with 'obviously bad code' are probably limited. Most people would probably try to fix the code as much as time allows, unless they really have no time.

Of course, there might be positive effects like raising the priority of refactoring and promoting code rewriting, but there are also potential disadvantages. If you leave bad code, not only does the risk of errors increase, but maintenance might actually become more difficult. Additionally, if there are many things to fix, it might feel like there's a lot to do, creating burden, and could have negative effects on development culture. Therefore, if using this strategy, you should be very careful internally.

### My Thoughts 2: About Safe Organizational Culture

Daniel Coyle, author of the book <What Makes the Best Teams Different>, says that 'Am I safe?' and 'Are we connected?' are the foundation of successful groups. This is because our brains still respond most sensitively to primitive fears.

In his book 'What Makes the Best Teams Different', he deals with this point more scientifically. Daniel Coyle says he spent 3 years looking for globally successful groups like pro sports teams, special forces, movie studios, comedy troupes, and jewel thieves. Successful groups had consistent behavioral patterns. Leaders created stable bonds with subtle signals saying 'we are connected', and accordingly, members fearlessly expressed even their weaknesses to each other. That is, he diagnosed that if any group produced excellent results, the reason was not because the team was smart but because it was safe.

The sense of safety in organizations that Daniel Coyle mentioned seems to make you feel its importance more when you experience it directly. (Of course, under the premise of maintaining appropriate boundaries) How important it is to be able to express opinions comfortably without worrying about others' reactions in the organization, to be able to rest when you need to rest. Furthermore, it can become the driving force that allows you to continue working at this company even when it's difficult. Conversely, how scary and stressful it is when you're in an organization that doesn't do this (...)

Anyway, the story went off track, but the author's belief that 'we won't be blamed even if we leave bad code', and the thought that good development culture also stems from organizational safety.

<br/>

## 🔖 13.4 Rules and Methods for Safely Making Code Look Bad

### Book Content

When making bad code look bad (i.e., making bad code noticeable), you must follow three rules.

1. Never damage correct information
2. Don't make future refactoring difficult
3. Results should be visible at a glance

Let's look at some common methods used to make bad code noticeable.

1. Using Enums: Use enums instead of type codes like boolean (+ advantage of being easy to find)
2. Using integers and strings as type codes: When using strings, text serves the same purpose as constant names
3. Putting magic numbers in code: Generally used when using constants inline (but be careful as there's risk of destroying information)
4. Putting comments in code: Using comments to preserve information and draw attention
5. Putting whitespace in code: Grouping code statements and grouping fields
6. Grouping items by name: Adding common prefixes to items to be grouped
7. Adding context to names: Adding common prefixes to names to make future data encapsulation easier
8. Making long methods: Warning signal for most developers
9. Passing many parameters to methods: Clearly identified on both the defining and calling sides
10. Using getters and setters: Should disappear when strengthening encapsulation classes

### My Thoughts

From experience, I felt the need for refactoring when I made long methods. For example, if there's a method called `calculateAndValidate`, I would often do separation work to follow the principle that one method should do only one thing.

Or when there were magic numbers, I would think 'Ah, this part hasn't been refactored yet' and do constantization, and I've done refactoring on other parts as well.

Looking back at experience while looking at these principles, I found the author's proposed principles and methods somewhat agreeable.

However, putting whitespace in code probably wouldn't be easily noticeable to other workers as a part that needs refactoring. Also, putting comments in code has the advantage of preserving information and is a way to quickly find where to refactor, but depending on preference, you could express it differently like raising it as an issue.

Anyway, the author's intention seems to be to do the opposite of the refactoring methods mentioned in the book so far, but to follow the three principles.

<br/>

## 📚 References

- [[Kim Ji-soo's Interstellar] "The way we treat each other is everything"](https://www.chosun.com/site/data/html_dir/2018/07/13/2018071302412.html)
