# Five Lines of Code - 5. Merging Similar Code

> Topic: Exposing structure through conditional arithmetic, introducing strategy pattern, and not creating interfaces with only one implementation

<br/>

## 🔖 5.4 Integrating Code Between Classes

### Book Content

UML (Unified Modeling Language) framework consists of various types of standard diagrams to convey properties about code. Among these, class diagrams show what relationships exist between class structures.

There's a famous saying that "All problems in computer science can be solved by introducing an indirect layer." This is exactly the interface. Detailed content is hidden under abstraction. John Carmack suggests we should be careful about abstraction by saying "Abstraction is allowing an increase in actual complexity in order to reduce perceived complexity."

### My Thoughts

At first, I had difficulty understanding UML concepts through the book, but I really grasped the purpose and meaning of UML through an article I read. The content was as follows:

"UML is a unified modeling language adopted as a standard by OMG (Object Management Group). That is, it's a standard language for creating models. A model is something that verifies in advance how to make something work well when actually creating it, and models are made to check designs when the cost is much less than the cost of making actual objects."

"Models in software are different from models in architecture, aviation, etc. When manufacturing hardware, whether there's a design or not makes a huge difference in cost, but creating models in software doesn't cost much, but it might not cost very little either. Sometimes it might even cost more than development. So UML is mainly used when there's something concrete to test and testing it with UML costs less than testing it with code. (e.g. understanding class dependencies, design for maintenance, communication with people, etc.)"

"As the saying goes that the best tool for drawing UML is paper and pen, it seems best to create it as needed rather than habitually."

<br/>

## 🔖 5.5 Integrating Similar Functions

### Book Content

Places with similar code can utilize **introduction of strategy pattern** to integrate these. Since two functions are not identical, they should be handled under the assumption that the first already exists and the second needs to be introduced. That is, introducing variation.

When introducing strategy, use **interface extraction from implementation** to allow adding variations.

<br/>

## 📚 References

- [UML: Class Diagram and Source Code Mapping](https://www.nextree.co.kr/p6753/)
