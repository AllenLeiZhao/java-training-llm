# Day02 - LLM Interaction Summary
**Date:** May 27, 2026
**Program:** RothurTech Java Training
**Topics:** Homework1, Immutable Class, Static Class, JVM, Exception Handling, PECS

---

## 1. Homework 1 Completion
Completed README.md for all 11 Collections questions. Key corrections:
- Q1: Added ordered/unordered distinction and common implementations
- Q3: Added thread safety clarification for LinkedHashMap and TreeMap (both NOT thread safe)
- Q6: Collections is a utility class, not a collection itself
- Q10: Corrected logical order:
  1. Only override equals() → equal keys may hash to different buckets → put fails
  2. Only override hashCode() → cannot identify correct key in same bucket → get returns null

---

## 2. Immutable Class Deep Dive

### Why private final
- `private` → prevents external modification
- `final` → prevents internal reassignment
- Without setter, `private` alone is sufficient, but `final` is a good defensive programming practice

### Defensive Copy vs Deep Copy
- `List<String>` → shallow defensive copy is enough (String is immutable)
- `List<Task>` → deep copy required (Task is mutable)
- Deep copy: iterate each element, `new Task()` and copy all fields

### Why Immutable is a Good Practice
- Thread-safe by nature, no locking needed
- Safe to use as HashMap key (hashCode never changes)
- Simpler and more reliable code
- Can be safely shared across references
- Enables String Pool reuse to save memory

---

## 3. Static Class
- Top-level class cannot be static
- Only nested classes can be static

    // Inner class: requires Outer instance, can access Outer instance variables
    Outer.Inner inner = outer.new Inner();

    // Static nested class: no Outer instance needed, cannot access Outer instance variables
    Outer.StaticInner si = new Outer.StaticInner();

- Builder Pattern commonly uses static nested class

---

## 4. JDK / JRE / JVM

    JDK
    ├── JRE
    │   ├── JVM
    │   └── Core Libraries
    └── Dev Tools (javac, jar, etc.)

    Hard Disk: stores .java / .class files (persistent)
    RAM: used during program execution (temporary)

    .java → (javac) → .class → (JVM) → execution

---

## 5. JVM Runtime Memory Architecture

| Area | Shared | Purpose |
|------|--------|---------|
| Method Area | ✅ All threads | Class info, static variables, constants |
| Heap | ✅ All threads | All object instances, GC works here |
| VM Stack | ❌ Per thread | Method calls, local variables |
| PC Register | ❌ Per thread | Tracks current executing instruction |
| Native Method Stack | ❌ Per thread | Executes native (non-Java) methods |

Flow: `.class → Class Loader → Runtime Data Memory Area`

---

## 6. Exception Handling Strategies

**Strategy 1: Handle at top level (recommended)**

    method1 (try-catch-finally)
      → method2 (throws)
        → method3 → throws exception

**Strategy 2: Handle at bottom level**

    method1
      → method2
        → method3 (try-catch-finally)

Strategy 1 is preferred: centralized exception handling, clear separation of concerns.
Spring Boot uses `@ControllerAdvice` for global exception handling.

---

## 7. PECS & Generics
- `? extends T`: read only — Producer Extends (unknown subtype, cannot write)
- `? super T`: write only — Consumer Super (unknown read type, cannot safely read)
- Java generics are invariant: `List<Manager> ≠ List<Employee>`
- `compareTo()` must return `int`, NOT `boolean`

---

## 8. Key Mistakes to Remember
- `compareTo()` returns `int`, not `boolean`
- Deep copy vs Defensive copy depends on whether list elements are mutable
- TreeMap and LinkedHashMap are both NOT thread safe
- `static` can only be applied to nested classes, not top-level classes