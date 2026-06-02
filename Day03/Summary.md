# Day03 - LLM Interaction Summary
**Date:** May 28, 2026
**Topics:** Immutable Class, Record, Deep Copy, Multithreading, ThreadPool, CompletableFuture

---

## 1. Immutable Class Deep Dive

### Deep Copy for Nested Mutable Objects
- `List<String>` → `List.copyOf()` sufficient
- `List<Task>` → need deep copy (iterate and new each element)
- `Map<String, List<String>>` → copy both outer Map and inner Lists
- Use `Collections.unmodifiableMap/List()` or `Map/List.copyOf()`

### Java 17 Record
- One-line immutable class creation
- Auto-generates: constructor, getters, equals, hashCode, toString
- Use compact constructor for deep copy
- Getter syntax: `e.name()` not `e.getName()`
- Interview bonus point to mention

### private vs private final
- `private` → prevents external access
- `final` → prevents internal reassignment
- Both needed even without setters (defensive programming)

---

## 2. Multithreading

### 4 Ways to Create Threads
1. extends Thread → occupies single inheritance, not recommended
2. implements Runnable → solves inheritance, no return value
3. implements Callable → return value + exception handling, needs FutureTask
4. ThreadPool → recommended, reuses threads

### Key Rules
- Must call `start()` not `run()` → `run()` is just a normal method call
- Cannot call `start()` twice
- Override `start()` must call `super.start()`

### Thread Lifecycle (6 States)
- BLOCKED: waiting for synchronized lock (passive)
- WAITING: actively released lock via wait(), needs notify()
- TIMED_WAITING: knows how long to wait (sleep/wait/join with timeout)

### ThreadPool 7 Parameters (口诀：核最活单队厂拒)
- corePoolSize, maximumPoolSize, keepAliveTime, TimeUnit
- workQueue (use ArrayBlockingQueue, not unbounded!)
- threadFactory, rejectionHandler

### Execution Flow
    task → core idle? → execute
         → core busy → queue → queue full? → create temp thread
                                           → max reached → reject

### 4 Rejection Policies
- AbortPolicy, DiscardPolicy, DiscardOldestPolicy, CallerRunsPolicy

### Predefined Pool Risks
- CachedThreadPool → unlimited threads → OOM
- FixedThreadPool → unbounded queue → OOM
- Always use custom ThreadPoolExecutor in production

---

## 3. Thread Safety

### 3 Approaches
1. Thread-safe data structures: ConcurrentHashMap, CopyOnWriteArrayList
2. CAS (optimistic): AtomicInteger, AtomicReference
3. synchronized (pessimistic): use block not method, minimize critical section

### Singleton Double-Checked Locking
- `volatile` prevents instruction reordering
- Double check prevents duplicate creation
- Eager loading: simpler alternative, naturally thread-safe

---

## 4. CompletableFuture

### vs Future
- Future: `get()` blocks main thread
- CompletableFuture: non-blocking, supports chaining

### Key APIs
- `runAsync/supplyAsync` → create async task
- `thenApply/thenAccept` → transform/consume result
- `thenCombine` → combine two independent futures
- `thenCompose` → chain dependent futures (like flatMap)
- `allOf/anyOf` → wait for all/fastest
- `exceptionally/handle` → exception handling

### thenApply vs thenCompose
- `thenApply`: next step returns normal value
- `thenCompose`: next step returns CompletableFuture (avoids nesting)

---

## 5. Coding Practice
- Third most frequent char: HashMap + MinHeap(size=3)
- Reverse string: two pointers, return `new String(array)` not `array.toString()`
- Two Sum all pairs: HashMap tracking frequency with containsKey check

## 6. Key Insights
- `array.toString()` returns memory address, use `new String(array)`
- Tasks queue BEFORE temporary threads are created
- `counting()` returns `Long` not `Integer`
- `containsKey()` before `get()` to avoid NPE