# Day05 - LLM Interaction Summary
**Date:** May 30 - June 1, 2026
**Topics:** Mock Interview Practice, CompletableFuture Deep Dive, Key Concepts Review, Q&A

---

## 1. Mock Interview Practice Results

| Topic | Score | Key Correction |
|---|---|---|
| HashMap | 95 | "array of LinkedList" not just "array of bucket" |
| OOP 4 Principles | 90 | Encapsulation=hide data, Abstraction=hide implementation |
| Interface vs Abstract | 85 | Interface fields: `public static final` not `private final` |
| == vs equals | 100 | Perfect ✅ |
| Checked vs Unchecked | 90 | Use "throws keyword" not "implements throws" |
| Thread Lifecycle | 85 | BLOCKED=waiting for lock, WAITING=voluntarily released lock |
| ArrayList vs LinkedList | 100 | Perfect ✅ |
| Functional Interface | 85 | Example syntax incorrect |
| Stream API | 90 | Add lazy evaluation point |
| synchronized vs Lock | 85 | ReentrantLock is class not interface |
| Singleton | 85 | Explain double-check reason |

**Overall: 88/100**

---

## 2. CompletableFuture Deep Dive

### API Categories
- **Create**: `runAsync()` (void), `supplyAsync()` (return value)
- **Transform**: `thenApply()` (sync), `thenApplyAsync()` (async)
- **Consume**: `thenAccept()` (no return, chain ends)
- **Combine**: `thenCombine()` (two independent), `thenCompose()` (dependent)
- **Wait**: `allOf()` (all complete), `anyOf()` (fastest)
- **Error**: `exceptionally()` (error only), `handle()` (both)

### Key Distinctions
- `thenApply` vs `thenCompose`: normal value vs CompletableFuture return (like map vs flatMap)
- `allOf` returns `Void` (multiple results, unclear which to return)
- `anyOf` returns `Object` (unknown which future completes first)
- `join()` vs `get()`: join throws unchecked, get throws checked

### When to Use
- Single async task → Callable
- Chaining/combining multiple async tasks → CompletableFuture
- Most commonly used: `supplyAsync` + `thenApply` + `thenAccept`

---

## 3. Key Concepts Clarified

### Collection Hierarchy
    Collection: List, Set, Queue(→Deque)
    Map: independent, not Collection (no Iterable, stores key-value pairs)
    Deque: double-ended queue, can also function as Stack
    ArrayDeque preferred over LinkedList for Deque

### PECS Full Name
    Producer Extends, Consumer Super
    extends → List produces data for you → read only
    super → List consumes data from you → write only

### Functional Interface
- Exactly ONE abstract method
- Any number of default and static methods allowed
- Static methods: called via interface name, cannot be overridden
- Used as utility/factory methods related to the interface

### Java Version Key Features

| Version | Must Know |
|---|---|
| Java 8 | Lambda, Stream, Optional, Functional Interface, Default method |
| Java 11 | var, String methods (isBlank/strip/repeat), HTTP Client |
| Java 17 | Record, Sealed Class, Pattern Matching instanceof, Text Block |
| Java 21 | Virtual Thread (millions of lightweight threads), Pattern Matching switch |

### OOM Causes & Solutions
- CachedThreadPool → unlimited threads → use custom bounded pool
- Static collections never cleaned → memory leak → clear unused entries
- Loading all data at once → use pagination
- Solution: increase `-Xmx`, fix leaks, analyze with heap dump/JProfiler

### ClassLoader Flow
    .class → Bootstrap → Extension → Application → JVM Memory
    Parent Delegation: always ask parent first, prevents core class tampering

### toString() Behavior
    Arrays: returns memory address (no override)
    StringBuilder/String: returns content (overrides toString())
    Always use new String(charArray) not charArray.toString()

---

## 4. Interview Strategy

### Answer Structure (Priority)
1. **Conclusion first** — always lead with direct answer
2. **Technical accuracy** — correct details
3. **Linguistic fluency** — clear communication
- Open-ended → start with "It depends", then break down scenarios

### Key Mistakes to Avoid
- `compareTo()` returns `int` not `boolean`
- `ReentrantLock` is a **class** not interface
- Interface fields are `public static final` not `private final`
- BLOCKED ≠ WAITING (different triggers and recovery)
- `array.toString()` → address, use `new String(array)`
- `counting()` returns `Long` not `Integer`
- "throws keyword" not "implements throws"
- Override `start()` must call `super.start()`

### Singleton Use Cases
- Logger, ThreadPool, DatabaseConnection, Configuration
- Key point: one shared instance saves memory, ensures consistent state

### Thread Lifecycle Key Points
- BLOCKED: no lock, waiting for someone to release
- WAITING: has released lock, waiting for notify()
- Can go RUNNABLE → TERMINATED directly if no blocking