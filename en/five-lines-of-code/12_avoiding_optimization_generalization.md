# Five Lines of Code - 12. Avoiding Optimization and Generalization

> Topic: Minimizing generality to minimize coupling, and viewing optimization from the perspective of invariants

<br/>

## 🔖 12.3 When and How to Optimize

### Book Content

To find reasons why optimization is needed, it's good to always set up automatic performance tests and only optimize when tests fail. Common test types are as follows.

- Benchmark Tests: Common in embedded or real-time systems that must provide responses within limited deadlines or intervals
- Load Tests: Verify throughput, common in web or cloud-based systems
- Performance Acceptance Tests: Check that performance doesn't suddenly degrade, completely isolated from external factors as long as there's consistency between runs

One of the refactoring goals is to localize invariants to make them clearer. Since optimization depends on invariants, this means it's easier to optimize well-decomposed code.

### My Thoughts

I didn't understand the sentence that optimization depends on invariants well, so I wrote it down understanding it as follows.

The above content emphasizes the importance of immutability in the software refactoring and optimization process. Let me recall it again for review. Localizing immutability means managing data so it doesn't change within code, and through this, you can increase code clarity, reduce errors, and easily perform performance optimization.

For example, let's say you implement a feature to manage user profile information by managing state as immutable objects. Use the `readonly` keyword to make properties like `name`, `age`, etc. unchangeable after creation, and declare the `hobbies` array as `ReadonlyArray` type to prevent array content from being changed.

The benefits of such immutability include improved code stability and maintainability since state doesn't change outside functions. Therefore, performance optimization work can also proceed effectively. That is, data with immutability guarantees referential transparency (program expressions can be replaced with the result of evaluating that expression), so optimization techniques like caching (storing frequently used data or calculation results in advance for fast access when needed) can be easily applied.

<br/>

## 🔖 12.3.6 Separating Optimized Code

### Book Content

It's rare that algorithms, concurrency, and caching are insufficient to satisfy performance tests. When insufficient, we switch to performance tuning, also called micro-optimization. At this time, we utilize small invariants that exist between runtime and desired behavior.

An example of tuning is using magic bit patterns. These are usually written in hexadecimal as Marbak numbers, making them much harder to read. Magic bit patterns often satisfy subtle performance differences that the used algorithm has.

### Reference Knowledge

The above content explains "micro-optimization," an additional optimization technique that can be applied **when performance goals are not met even after achieving performance optimization through algorithm and concurrency management** in software development. This optimization particularly focuses on improving performance by utilizing small changes at runtime. Using "magic bit patterns" is one example of such micro-optimization.

(It doesn't seem like very important content, but just out of curiosity, let me organize it..) Magic bit patterns are mainly used in calculations performed using bit operations. For example, very quickly checking whether a number is a power of 2 is said to be one of the common operations needed in video games.

<br/>

## 🔖 12.3.2 Optimization According to Constraint Theory

### Book Content

Optimizing downstream workers (next workers) of bottleneck workers doesn't affect overall performance because downstream workers can't receive input fast enough. Only by optimizing bottleneck workers can system performance be affected.

When you optimize bottlenecks, you encounter new bottlenecks. Fortunately, software has a solution called 'resource pooling'. Resource pooling means placing all available resources in a common pool that can be used when needed. Therefore, maximum possible capacity is provided to the bottleneck point. This approach can be implemented externally at the service level through load balancers or within applications through thread pooling.

### My Thoughts

Resource pooling is said to be a very useful strategy especially in environments requiring high performance. This helps improve overall system performance by efficiently using and reusing resources when needed. For example, there might be cases where a server needs to handle a large number of concurrent requests. When requests for resources like DB connections or thread usage surge like this, using resource pooling allows you to create resources in advance and store them in a pool, so you can quickly allocate and return resources whenever requests occur.

There's also the concept of 'thread pool' related to resource pooling. If there are many threads, overhead from thread switching increases. Therefore, you create several threads in advance, and when there's work for threads to process, you request processing from that thread. This way, since threads are created in advance, thread creation and termination operations don't occur frequently. What's important here is also 'reusing threads'.

I found out that thread pools can actually be seen as an example of resource pooling strategy. (The term resource pooling is a more general concept used for DB connections, memory buffers, etc.)

<br/>

## 📚 References

- Book <Secrets of Computer Systems>
- [Pipeline Optimization: How to improve pipeline performance and scalability using various technologies and methods](https://fastercapital.com/ko/content/%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8-%EC%B5%9C%EC%A0%81%ED%99%94--%EB%8B%A4%EC%96%91%ED%95%9C-%EA%B8%B0%EC%88%A0%EA%B3%BC-%EB%B0%A9%EB%B2%95%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8%EC%9D%98-%EC%84%B1%EB%8A%A5%EA%B3%BC-%ED%99%95%EC%9E%A5%EC%84%B1%EC%9D%84-%EA%B0%9C%EC%84%A0%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95.html)
