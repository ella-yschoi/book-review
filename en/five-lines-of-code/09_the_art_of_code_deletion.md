# Five Lines of Code - 9. The Art of Code Deletion

> Topic: Removing everything that doesn't help (strangler fig pattern, spike&stabilize pattern)

<br/>

## 🔖 9.4.1 Strangler Fig Pattern

### Book Content

To gain insight into legacy code, you need to know how often each part is called. You also need to know how closely the legacy code is connected to the rest of the software.

To help with this process, you can use Martin Fowler's Strangler Fig Pattern. The most frequently called parts almost certainly need to be migrated, and the least frequently called parts are likely to be deleted, so handle these small branches first and move to the trunk where there are difficult decisions. You need to critically evaluate the least used or always failing code to determine whether it has important or strategic functionality.

### My Thoughts

When working at a company, I'll probably spend more time maintaining existing code or features and understanding legacy code rather than creating new features. It would be good to remember the above statement well at that time.

By the way, the AWS guide explains the Strangler Fig Pattern as follows:

When using the strangler fig pattern, follow these best practices. This will allow you to scale applications independently and deploy more smoothly.

- Choose components with good test coverage and low technical debt. Starting with these components gives teams confidence during the modernization process.
- Choose components with scalability requirements and start with one of these components.
- Choose components where business requirements change frequently and are deployed frequently.

However, the disadvantages of the Strangler Fig Pattern are explained as follows, so it would be good to refer to:

- Not suitable for small systems with low complexity and small size.
- Cannot be used in systems where requests to backend systems cannot be intercepted or routed.
- Proxy or facade layers can become single points of failure or performance bottlenecks if not properly designed.
- To quickly and safely rollback to the previous way of doing things when problems occur, you need a rollback plan for each refactored service.

<br/>

## 🔖 9.10 Code Deletion for Library Removal

### Book Content

Choose libraries from high-quality suppliers and trust that company's internal quality security requirements.

A simple solution to mitigate the security risks mentioned in the previous section is to make dependencies visible, then classify each library as either for improvement or for importance. Using this method, you reduce dependencies and ultimately reduce dependence on libraries. However, be careful when promoting from improvement libraries to important libraries.

### My Thoughts

#### Understanding Dependency Visualization

Clearly identify and document all external libraries (dependencies) used in the project. This makes it easy to understand which libraries are included in the project and what role each library plays.

What problems might occur if documentation is not done well? If the version of a library in use is outdated or security patches are not applied, it may be vulnerable to security attacks that exploit this. Or when multiple libraries are used in a project, version conflicts, compatibility issues, or duplicate usage problems may occur. If such conflicts occur, unexpected bugs will occur and considerable time and resources will be consumed in debugging.

#### Understanding Library Importance Classification

Classify each library as 'for improvement' and 'for importance'. This means prioritizing based on the importance and necessity of the library.

- Improvement libraries: Those that provide convenient features to the project or help simplify the development process, but are not directly essential to core functionality or system operation.
- Important libraries: On the other hand, these refer to essential libraries that are deeply involved in the project's core functionality or directly related to system stability.

These two have very different characteristics than you might think, so as described in the book, you should be careful when promoting from improvement libraries to important libraries.

<br/>

## 📚 References

- [Software, Faster • Dan North • GOTO 2016](https://www.youtube.com/watch?v=USc-yLHXNUg)
- [AWS Guide - Strangler Fig Pattern](https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-aspnet-web-services/fig-pattern.html)
- [AWS Guide - Pros and Cons of Strangler Fig Pattern](https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-decomposing-monoliths/strangler-fig.html)
