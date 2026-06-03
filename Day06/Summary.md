# Day06 - LLM Interaction Summary
**Date:** June 2, 2026
**Topics:** Concept Clarifications, Error vs Exception, Collection Hierarchy, Final Review

---

## 1. Key Concept Corrections

### OOM is Error, not Exception
- `OutOfMemoryError` extends `Error`, not `Exception`
- `Error` = JVM-level, unrecoverable (OOM, StackOverflow)
- `Exception` = business-level, recoverable
- Cannot and should not try to catch Errors

### Interface Fields
- Interface fields are `public static final` (constants)
- NOT `private final` as previously mistaken
- Automatically applied even without explicit declaration

### ReentrantLock
- Is a **class** that implements `Lock` interface
- NOT an interface itself
- Must always `unlock()` in finally block

---

## 2. Collection Hierarchy Clarified

    Collection
    ├── List (ArrayList, LinkedList, Stack)
    ├── Set (HashSet, LinkedHashSet, TreeSet)
    └── Queue
        └── Deque (ArrayDeque, LinkedList) ← Queue의 subinterface

    Map (independent from Collection)
    ├── HashMap, LinkedHashMap, TreeMap, HashTable

- `Deque` is subinterface of `Queue`, not standalone
- `ArrayDeque` preferred over `LinkedList` for Deque operations
- `Map` is independent: stores key-value pairs, not Iterable

---

## 3. Miscellaneous Clarifications

### throws vs throw
- `throws` on method signature: declares possibility of exception
- `throw` in method body: actually throws the exception
- Having `throws` doesn't mean exception will always be thrown

### Callable Exception Handling
- `call()` has `throws Exception` in interface definition
- Caller receives exception wrapped in `ExecutionException`
- Use `e.getCause()` to get original exception

### compareTo() Contract
- Must return `int` not `boolean`
- Positive: this > other
- Zero: this == other
- Negative: this < other

---

## 4. Interview Reminders

### Common Mistakes to Watch
- OOM is **Error** not Exception
- `Deque` is under `Queue`, not standalone
- `throws` = declaration, `throw` = actual throwing
- All exceptions occur at **runtime**; "checked" = compiler enforces handling

### Answer Strategy
- Always lead with conclusion
- "It depends" must be followed by conditions
- Connect concepts to real use cases