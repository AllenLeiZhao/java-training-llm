# Day12 - LLM Interaction Summary
**Date:** June 10, 2026
**Topics:** Mock Interview Q&A Review, Spring Boot Core Concepts, Debug Flow, Java 11 Features

---

## 1. Mock Interview Questions Review (Day12 - 10 Questions)

### Q1. How to write a RESTful API in Spring Boot?
- Follow three-tier architecture: Controller → Service → Repository
- Map HTTP methods to CRUD operations: POST=create, GET=read, PUT=update, DELETE=delete
- Two rules: URL is a noun never a verb; every response returns payload + HTTP status code
- Used in Student Management project with five RESTful endpoints
- Key annotations: @RestController, @RequestMapping, @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PathVariable, @RequestBody, @ResponseEntity

### Q2. How did you debug? (Instructor: David Lu's version)
Correct flow in order:
1. Check the log (HTTP status code + error/exception message)
2. Look at the request that triggered the error
3. Reproduce in production-like environment
4. Switch to dev environment (request payload + Postman + local IntelliJ)
5. Attach debugger + set breakpoints

Key point: Start from logs, not from the debugger directly.

### Q3. What is a fair lock?
- Fair lock guarantees FIFO (First In First Out) order for thread acquisition
- Prevents thread starvation
- Java implementation: new ReentrantLock(true)
- Trade-off: slower than non-fair lock due to queue maintenance overhead
- Use only when ordering matters and starvation is unacceptable

### Q4. New features in Java 11? (Instructor priority order)
1. Standardized HTTP Client (java.net.http package, supports HTTP/2, sync and async)
2. ZGC — low-latency garbage collector, introduced as experimental, keeps pause times short even on large heaps
3. Epsilon GC — no-op collector, allocates but never reclaims memory, used for performance testing/benchmarking
4. var keyword for lambda parameters
5. New String methods: isBlank(), strip(), lines(), repeat()

### Q5. Design a task management application (CRUD)
- Clarify requirements first: web or local? single or multi-user? what fields?
- Model Task entity: id, title, description, status (TODO/IN_PROGRESS/DONE), dueDate
- Three-tier: Controller (REST endpoints) → Service (business logic) → Repository (persistence)
- Add validation, global exception handling, pagination for production-readiness
- Key signal: clarify first, state approach, then code

### Q6. Locking scheme: thread calling method1() waits until another thread calls method2()
- Thread-signaling problem
- Solution: wait() and notify() on shared lock object inside synchronized blocks
- method1() calls wait() → releases lock and pauses
- method2() does work → calls notify() → wakes waiting thread
- Always use while loop (not if) to guard against spurious wakeups
- Modern alternatives: ReentrantLock + Condition, or CountDownLatch for one-time event

### Q7. Recursion vs Iteration
- Recursion: calls itself, needs base case, adds stack frames → risk of StackOverflowError
- Iteration: uses loop, constant stack space, generally faster
- Recursion better for tree/graph traversal; iteration better for deep/simple linear inputs

### Q8. LinkedHashMap vs HashMap
- Both O(1) average access
- HashMap: no ordering guarantee
- LinkedHashMap: extends HashMap, adds doubly-linked list → maintains insertion order by default
- LinkedHashMap access-order mode (constructor param true) → perfect for LRU cache

### Q9. Group people by age (key=age, value=list of people)
- Use Stream API: Collectors.groupingBy(Person::getAge)
- Result: Map<Integer, List<Person>>
- Downstream collectors: counting() for count per age, mapping() for list of names

### Q10. How can you use Optional?
- Java 8 container for potentially null values, avoids NullPointerException
- Creation: Optional.of() (non-null), Optional.ofNullable() (allows null), Optional.empty()
- Consumption: orElse(), orElseThrow(), ifPresent(), map()
- Best practice: use for return types, not fields or parameters; avoid calling get() directly

---

## 2. Key Concepts Clarified

### LTS (Long-Term Support)
- LTS versions get official updates and security patches for many years
- Java LTS versions: 8, 11, 17, 21
- Non-LTS versions supported only 6 months
- Enterprise always uses LTS for stability

### Java LTS Version Features Summary
- Java 8: Lambda, Stream API, Optional, new Date/Time API, default methods, method references
- Java 11: HTTP Client, ZGC, Epsilon GC, String methods, var in lambda
- Java 17: Records, Sealed classes, Switch expressions, Text blocks, Pattern matching instanceof
- Java 21: Virtual Threads (Project Loom), Pattern matching for switch, Sequenced Collections

### RESTful API URL Design
- Standard rule: URL = noun, never verb (HTTP method expresses the action)
- Accepted exception: /login and /logout (actions that don't map cleanly to a resource)
- In interviews: state the rule first, then mention the exception if asked

### Singleton Pattern — Where Used in Spring
- Spring beans are singleton by default (default bean scope)
- Service and Repository beans are singleton because they are stateless
- Stateless = safe to share one instance across all requests
- Common singleton use cases: database connection pool, configuration manager, logger, cache manager

### this. vs Spring Proxy (AOP)
- this. calls bypass the Spring proxy entirely
- AOP annotations like @Transactional won't trigger if called via this.
- Must call through the injected Spring bean (proxy) for AOP to work
- self in instructor's context = calling through the Spring-managed proxy, not Python's self

### Spring Actuator
- Monitoring tool exposing built-in HTTP endpoints
- Key endpoints: /actuator/health, /actuator/metrics, /actuator/loggers
- Enable: add spring-boot-starter-actuator dependency
- Configure: management.endpoints.web.exposure.include=* in application.properties
- Production: only expose health and metrics (env leaks sensitive info)
- Stack: Actuator (exposes) → Prometheus (collects) → Grafana (visualizes)

### Bean Registration Annotations
- @Component: generic, auto-scanned
- @Service: service layer semantic
- @Repository: data layer semantic + database exception translation
- @Controller: web layer, returns views
- @RestController: @Controller + @ResponseBody, returns JSON
- @Bean: on a method inside @Configuration class, for third-party classes whose source code cannot be modified
- @Configuration: marks class as configuration, contains @Bean methods

### SOLID Principles
- S: Single Responsibility — one class does one thing (reason for three-tier architecture)
- O: Open/Closed — open for extension, closed for modification (use interfaces)
- L: Liskov Substitution — subclass can replace parent without breaking behavior
- I: Interface Segregation — keep interfaces small and focused
- D: Dependency Inversion — depend on abstractions not concretions (basis of Spring DI)

### Many-to-Many REST API Design (User and ToDoItem)
- Introduce junction entity (e.g., UserTask) with userId and taskId as foreign keys
- JPA: use @ManyToMany or two @OneToMany through junction entity
- Endpoints: CRUD for /users, CRUD for /todos, nested /users/{id}/todos for the relationship
- Nested URL keeps structure clean and RESTful

### PATCH vs PUT — Idempotency
- PUT: always idempotent (replaces entire resource)
- PATCH: not guaranteed idempotent (depends on implementation)
  - "Set name to John" → idempotent
  - "Increment age by 1" → not idempotent

---

## 3. HW11 Assignment
- HW11a: Add AOP, Validation, Actuator, Prometheus, Grafana to project; create PR; add PR URL to .md file
- HW11b: Prepare script → Record video → Upload to S3 → Attach S3 link in .md → Upload .md to GitHub repo

---

## 4. Key Insights
- Debug flow starts from logs, not from the debugger — this is David Lu's emphasized order
- Fair lock = FIFO, non-fair lock = faster but can starve threads
- Java 11 new features should be mentioned in priority order: HTTP Client → ZGC → Epsilon GC → others
- Singleton is safe only when the bean is stateless
- this. bypasses Spring proxy — AOP and @Transactional won't work inside same class via this.
- PATCH idempotency depends on implementation, not guaranteed like PUT