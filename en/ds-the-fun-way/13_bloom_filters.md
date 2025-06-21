# 📌 Data Structures the Fun Way - 13. Bloom Filters

> Probabilistic data structures that can quickly determine inclusion while using little space

<br/>

## 🔖 13.1. What is a Bloom Filter?

### Definition

- An extension concept of hash tables, a data structure that quickly determines whether specific data exists
- Uses a bit array and multiple hash functions to store data
- Fast but may have probabilistic errors (false positives)

### Characteristics

- Space efficient: Uses little memory as it doesn't store data directly, only changes bits
- Fast search: Can query in near O(1) through hash functions
- No false negatives: There's no case where existing data shows as "not existing"

<br/>

## 🔖 13.2. How Bloom Filters Work

### Adding Data (Add)

- Transform input value with `k` hash functions and set the corresponding bit positions to `1`

### Checking Data (Check)

- Transform input value with `k` hash functions and check if all corresponding bits are `1`
- If all bits are `1`, "likely exists" (not certain if it actually exists)
- If any bit is `0`, "does not exist" (accurate result)

### False Positive Possibility

- If other elements have the same hash values, you may incorrectly judge that non-existent data exists

<br/>

## 🔖 13.3. Important Elements in Bloom Filters

### Why False Positives Occur

- Because bloom filters don't store data itself, only use bit arrays and hash functions
- If the bit array is full or the number of hash functions is inappropriate, collisions increase, leading to increased false positives

### Methods to Reduce False Positives

1. Increase bit array size (`m`) → Using more space reduces collisions
2. Use appropriate number of hash functions (`k`) → Too many or too few worsens performance
3. Optimal settings for data count (`n`) → Too much data increases false positives

### Formula for Optimal Number of Hash Functions

```plaintext
k = (m/n) * ln(2)
```

- `m`: Bit array size
- `n`: Number of elements to insert
- `k`: Number of hash functions

<br/>

## 🔖 13.4. Bloom Filter vs. Hash Table

| Comparison Item          | Bloom Filter                 | Hash Table                        |
| ------------------------ | ---------------------------- | --------------------------------- |
| **Memory Usage**         | Low                          | High                              |
| **Accuracy**             | Has false positives          | Returns accurate values           |
| **Deletion Capability**  | Cannot delete (basically)    | Can delete                        |
| **Speed**                | Very fast (O(1))             | Fast (O(1), O(n) with collisions) |
| **Application Examples** | Caching, search optimization | Key-value storage, data lookup    |

<br/>

## 🔖 13.5. Use Cases of Bloom Filters

- Search engines: Quickly filter frequently searched keywords to improve search speed
- Cache systems: Determine if data exists in cache to prevent unnecessary disk access
- Spam filters: Check if email addresses are in blacklists
- Network security: Filter malicious sites, IP addresses

<br/>

## 🎯 Key Summary of Bloom Filters

- Data structures that can quickly query while using little space
- False positives may occur, but no false negatives
- Performance can be improved by optimizing bit array size (`m`), number of hash functions (`k`), and data count (`n`)
- Usefully used in systems requiring fast filtering (search engines, caches, security)
