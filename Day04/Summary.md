# Day04 - LLM Interaction Summary
**Date:** May 29, 2026
**Topics:** Homework 3-5, Functional Programming, Stream API Coding

---

## 1. Homework 3 - Java Core Topics
- Access modifiers: public/protected/default/private scope
- Static: variables, methods, blocks, nested classes only
- ClassLoader: Bootstrap → Extension → Application, Parent Delegation
- Checked vs Unchecked exceptions
- finally vs final vs finalize
- try-with-resources: AutoCloseable, auto cleanup
- Custom exceptions: extend RuntimeException, add error code

## 2. Homework 4 - Functional Programming

### Functional Interface
- Exactly one abstract method
- Can have default and static methods
- `@FunctionalInterface` optional but recommended

### 4 Built-in Interfaces

| Interface | Input | Output | Stream use |
|---|---|---|---|
| Predicate<T> | T | boolean | filter() |
| Function<T,R> | T | R | map() |
| Consumer<T> | T | void | forEach() |
| Supplier<T> | none | T | orElseGet() |

### Predicate Composition
- `and()`, `or()`, `negate()` → combine conditions

### Method References
- Static: `Integer::parseInt`
- Instance: `Student::getName`
- Constructor: `Student::new`

### Stream API Key Operations
- `mapToDouble().sum()` for numeric aggregation
- `groupingBy()` for frequency counting
- `toMap()` with merge function for duplicate keys
- `flatMap()` to flatten nested collections
- `chars().mapToObj(c -> (char)c)` for char[] streams

### Optional
- `ofNullable`, `orElse`, `orElseGet`, `orElseThrow`
- Avoids NullPointerException elegantly

## 3. Homework 5 - Multithreading Q&A

### Key Comparisons
- Future vs CompletableFuture: blocking vs non-blocking
- synchronized vs ReentrantLock: auto vs manual, less vs more flexible
- wait() vs sleep(): releases lock vs holds lock
- ArrayBlockingQueue vs LinkedBlockingQueue: bounded vs unbounded

### OOM Solutions
- Increase heap size with JVM args
- Fix memory leaks in static collections
- Use bounded ThreadPool
- Use pagination for large data

## 4. Java Version Features

| Version | Key Features |
|---|---|
| Java 8 | Lambda, Stream, Optional, Functional Interface |
| Java 11 | var, String methods, HTTP Client |
| Java 17 | Record, Sealed Class, Pattern Matching, Text Block |
| Java 21 | Virtual Thread, Pattern Matching for switch |

## 5. Key Insights
- Interface fields are `public static final` not `private final`
- `throws` is a keyword, not "implements throws"
- All exceptions occur at RUNTIME; "checked" = compiler enforces handling
- Static methods in interface: called via interface name, cannot override