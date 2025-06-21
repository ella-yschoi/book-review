# Data Structures the Fun Way - 11. Caching

> Data structures that store some data closer to where computation occurs to reduce data access costs

<br/>

## 🔖 11.1 Introduction to Caching

### Terminology

- It's important to understand how the trade-off between speed and space size according to memory hierarchy affects algorithm performance.
- Cache accesses expensive data (data that takes more time to read) one step ahead of us.
- Before calling remote servers or accessing local hard drives, it checks if data is stored locally in the cache.
- When data is found in the cache, it's called a hit.
- When cache hits, you can stop searching and avoid calling more expensive storage.
- Note that when data cannot be found in the cache, it's called a miss.

### Various Caching Methods

- Various expiration strategies are used to determine which data to replace.
- Methods like counting how often data has been accessed,
- Methods for predicting whether data will be used again in the near future, etc.

<br/>

## 🔖 LRU Expiration and Cache

### LRU

- LRU maintains recently used information in the cache.
- LRU cache is a way that well shows the type of trade-off we face when using cache.
- The intuitive idea of this cache strategy is that there's a high probability of accessing information that was recently needed again.
- For example, if you keep accessing the same set of pages, it's advantageous to store unchanging elements of these pages locally.
- When the cache is full, it removes the least recently used item to make space.

### Building LRU Cache

- Cache using LRU strategy must support two operations: finding arbitrary elements and finding elements not recently used.
- And both operations must be performed quickly.
- This is because the purpose of cache is to accelerate data reading operations.
- You can create LRU cache using hash tables and queues.

<br/>

## 🔖 11.3 Other Expiration Strategies

### MRU Strategy, LFU Strategy

- MRU (Most Recently Used), LFU (Least Frequently Used)
- The previously introduced LRU is a good expiration strategy when common usage patterns are intensively observed among specific cache items, and when the distribution of cached elements is expected to change over time.
- In comparison, MRU expiration strategy can be a good approach for items with low repetition frequency.
- And LFU strategy can track the total number of accesses instead of considering when elements were last accessed. This method is advantageous when cached items are maintained stably.
