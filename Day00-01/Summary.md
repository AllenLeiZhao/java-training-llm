# Day00 - LLM Interaction Summary
**Date:** May 25-26, 2026  
**Program:** RothurTech Java Training  
**Topics:** Environment Setup, Java Collections, Algorithm Practice

---

## 1. Environment Setup (Completed ✅)
All tools installed and screenshots uploaded to GitHub.

| Tool | Version | Status |
|------|---------|--------|
| Java | OpenJDK 21.0.3 (Corretto) | ✅ |
| IntelliJ IDEA | 2026.1.2 Ultimate | ✅ |
| Docker | Desktop | ✅ |
| PostgreSQL | Docker container | ✅ |
| MongoDB | Docker container | ✅ |
| Redis | Docker container | ✅ |
| Cassandra | Docker container | ✅ |
| Elasticsearch | Docker container | ✅ |
| Git | 2.24.3 | ✅ |
| Maven | 3.9.16 | ✅ |
| Gradle | 9.5.1 | ✅ |
| AWS S3 | allen-2026-learning bucket | ✅ |
| OBS | 32.1.2 | ✅ |
| Codex CLI | 0.133.0 | ✅ |
| LinkedIn | 500+ connections | ✅ |

GitHub: https://github.com/AllenLeiZhao/java-training-homework

---

## 2. Java Collections Q&A

### List vs Set
- List: ordered, allows duplicates, accessible by index
- Set: unordered, no duplicates, no index
- Key terms: "maintains insertion order" vs "does not guarantee order"

### LinkedList vs ArrayList
- ArrayList: backed by dynamic array, O(1) random access, O(n) insert/delete
- LinkedList: backed by doubly linked list, O(n) random access, O(1) insert/delete at head/tail
- ArrayList expands to 1.5x when full, copies all elements
- In practice: ArrayList used more often (random access more common)

### Map Interface
- Stores key-value pairs, key cannot be duplicated
- HashMap: unordered, allows one null key, not thread-safe
- TreeMap: sorted by key, no null key
- LinkedHashMap: maintains insertion order

### How HashMap Works
- Internal structure: array of LinkedList, converts to Red-Black Tree when bucket size > 8
- put(): hashCode() → find bucket index → equals() → insert or overwrite
- Default capacity: 16, load factor: 0.75, resizes to 2x when exceeded
- Resize triggers rehash of all elements

### Hash Collision
- Two different keys produce the same hashCode → mapped to same bucket
- Java resolves with Chaining: LinkedList in same bucket, converts to Red-Black Tree after 8 elements
- hashCode same ≠ same key; equals same → hashCode must be same

### Collections (utility class)
- Collection (no s): interface, parent of List/Set/Queue
- Collections (with s): utility class with static methods
- Common methods: sort(), reverse(), shuffle(), max(), min(), frequency(), unmodifiableList()

### Immutable Class
- Once created, state cannot be modified
- Examples: String, Integer, Long, Double
- How to create:
  1. Declare class as `final`
  2. All fields `private final`
  3. No setters
  4. Defensive copy for mutable fields in constructor and getters
- Defensive copy vs Deep copy:
  - Defensive copy: protect from external modification (shallow copy enough if fields are immutable)
  - Deep copy: recursively copy all nested mutable objects

### HashTable vs HashMap vs ConcurrentHashMap
| | HashTable | HashMap | ConcurrentHashMap |
|---|---|---|---|
| Thread-safe | ✅ (object level lock) | ❌ | ✅ (bucket level CAS+synchronized) |
| Null key | ❌ | ✅ (one) | ❌ |
| Performance | Poor | Best | Middle |
| Use today | ❌ Legacy | Single-thread | Multi-thread |

### String vs StringBuilder vs StringBuffer
| | String | StringBuilder | StringBuffer |
|---|---|---|---|
| Mutable | ❌ | ✅ | ✅ |
| Thread-safe | ✅ | ❌ | ✅ |
| Performance | Slow | Fastest | Slower than SB |
| Use case | General | Single-thread | Multi-thread |

Note: StringBuffer guarantees atomicity of individual operations but NOT order across threads.

### Why Override hashCode and equals Together
- Without hashCode: same key maps to different bucket → cannot find
- Without equals: finds bucket but cannot identify correct key → returns null
- Java contract: if a.equals(b) == true, then a.hashCode() must == b.hashCode()

### Collections API Practice
- List: add(), get(), remove(), size()
- Set: add(), contains()
- Queue: offer(), peek(), poll()
- Map: put(), get(), containsKey(), remove(), entrySet()

---

## 3. Generics & Wildcards (from class)

### PECS Principle
- Producer Extends, Consumer Super
- `? extends T`: read only (you know it's some subtype of T, but not which one → can't write)
- `? super T`: write only (you know it can hold T or larger → can't safely read)

### Why Wildcards?
- Java generics are invariant: List<Manager> ≠ List<Employee>
- Wildcards make methods more flexible

---

## 4. Algorithm Practice

### Two Sum - Basic (O(n), single answer)
```java
private static int[] twoSum(int[] array, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < array.length; i++) {
        if (map.containsKey(target - array[i])) {
            return new int[]{map.get(target - array[i]), i};
        }
        map.put(array[i], i);
    }
    return null;
}
```

### Two Sum - All Results with Duplicates
```java
private static List<int[]> twoSum(int[] array, int target) {
    List<int[]> result = new ArrayList<>();
    Map<Integer, List<Integer>> map = new HashMap<>();
    for (int i = 0; i < array.length; i++) {
        if (map.containsKey(target - array[i])) {
            List<Integer> list = map.get(target - array[i]);
            for (int num : list) {
                result.add(new int[]{i, num});
            }
        }
        List<Integer> cur = map.getOrDefault(array[i], new ArrayList<>());
        cur.add(i);
        map.put(array[i], cur);
    }
    return result;
}
```

Key insight: use `map.containsKey()` over `map.get() != null` for clarity.

---

## 5. Key Insights & Mistakes to Remember
- `compareTo()` must return `int`, not `boolean`
- `Collections` (with s) is a utility class, not a collection itself
- StringBuffer is thread-safe per operation but cannot guarantee order across threads
- Defensive copy ≠ Deep copy (depends on whether nested objects are mutable)
- Use `map.containsKey()` instead of `map.get() != null`

---

## 6. Next Steps
- Complete Homework 1 (Collections Q&A + code)
- Complete Homework 2 (JVM, GC, Java basics)
- Set up Google Doc for daily class notes
- Daily English listening practice (30-60 min)