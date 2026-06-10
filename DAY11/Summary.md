# Day11 - LLM Interaction Summary
**Date:** June 9, 2026
**Topics:** AOP, Spring MVC Flow, Bean Injection, HW10 Completion

---

## 1. AOP (Aspect Oriented Programming)

### What is AOP
AOP separates cross-cutting concerns like logging, security, and transaction
management from business logic. Instead of writing repetitive code in every
method, you define these concerns once in an Aspect class and Spring
automatically applies them to specified methods.

### Key AOP Concepts
- Aspect: a separate class containing cross-cutting logic (@Aspect + @Component)
- Pointcut: defines where and when the aspect should be applied (which methods)
- JoinPoint: the specific method currently being intercepted at runtime
- Advice: the actual action to execute (@Before, @After, @Around)
- Target: the object instance that owns the method being intercepted

### Advice Types
- @Before: executes before the method
- @After: executes after the method regardless of success or failure
- @AfterReturning: executes only on successful return
- @AfterThrowing: executes only when exception is thrown
- @Around: wraps entire method, most powerful, requires proceed()

### Execution Order
    Success: @Around start → @Before → method → @AfterReturning → @After → @Around end
    Failure: @Around start → @Before → method → @AfterThrowing  → @After → @Around end

### @Around and proceed()
- proceed() must be called to execute the original method
- If proceed() is not called, original method is skipped entirely
- Can be intentional for use cases like permission checks
- Must use ProceedingJoinPoint (not JoinPoint) for @Around

### Pointcut Expression
    @Pointcut("execution(* com.example.service.*.*(..))")
    //                    ↑  ↑               ↑ ↑  ↑
    //              return  package        class method params
    - * = any return type
    - service.* = any class in service package
    - *.(..) = any method with any parameters
    - Method name after @Pointcut is just an identifier, can be anything

### Why @Component on Aspect
- @Aspect marks the class as an aspect
- @Component registers it as a Spring Bean
- Without @Component, Spring cannot detect it and AOP won't work

### AOP vs OOP
- OOP organizes code vertically into classes and objects
- AOP handles cross-cutting concerns horizontally across multiple classes
- AOP complements OOP, not replaces it
- Spring uses both together

### AOP Implementation in Student Project
- Created LoggingAspect class in aspect package
- Pointcut cuts all methods in service package
- @Before logs method name before execution
- @After logs method name after execution
- @Around measures and logs execution time
- StudentServiceImpl unchanged — AOP injected automatically via proxy

---

## 2. Spring MVC Flow

### Complete Request Flow
    Client (Postman)
      ↓
    API Gateway (auth, routing)
      ↓
    Load Balancer
      ↓
    DispatcherServlet (front controller, receives all requests)
      ↓
    HandlerMapping (finds correct Controller and method)
      ↓
    Controller (handles HTTP, calls Service)
      ↓
    Service (business logic, calls Repository)
      ↓
    Repository (database operations)
      ↓
    HttpMessageConverter (converts Java object to JSON)
      ↓
    HTTP Response back to Client

### How Spring Identifies the Handler Method
- HTTP method + URL together uniquely identify the handler method
- GET /api/students → getAllStudents()
- GET /api/students/{id} → getStudentById()
- POST /api/students → createStudent()
- Each combination must be unique, duplicate causes startup error
- Method name has no meaning to Spring, only for developer readability

### ResponseEntity
- Wraps return value with HTTP status code
- ResponseEntity.ok(data) → 200 + data
- new ResponseEntity<>(data, HttpStatus.CREATED) → 201 + data
- Spring uses HttpMessageConverter to serialize Java object to JSON

---

## 3. Bean Injection Deep Dive

### Why Interface Can Be Injected
- Interface itself is not a Bean
- Spring finds the implementation class that implements the interface
- Injects the implementation class instance
- Controller declares StudentService type, Spring injects StudentServiceImpl

### Why Declare as Interface Type
- Loose coupling: if implementation changes, Controller doesn't need to change
- Follows Dependency Inversion Principle: depend on abstraction not concretion

### Spring Injection by Type
- Spring searches IoC container for Bean matching the declared type
- If one match found → inject directly
- If multiple matches → need @Primary or @Qualifier
- Implementation class name doesn't matter, only the interface type and @Service

### Method Name Has No Meaning to Spring
- Spring only cares about HTTP method + URL mapping
- Method names are for developer readability only
- Same applies to class names for injection — only type matters

---

## 4. Project Structure Review

### DTO
- Simple data container, no business logic
- Only needs field definitions and Lombok annotations
- No @Service, @Entity, or any Spring annotations needed
- Purpose: transfer data between layers, hide sensitive Entity fields

### Repository
- Just an empty interface extending JpaRepository
- Spring Data JPA auto-generates implementation at runtime
- JpaRepository<Entity, PrimaryKeyType> provides all CRUD methods
- No implementation needed

### AOP Proxy Pattern
- Spring creates a proxy object wrapping the target Bean
- When you call Service method, you're actually calling the proxy
- Proxy executes Before/After logic then delegates to real method
- Target class is completely unaware of AOP

---

## 5. Git Workflow

### PR (Pull Request) Flow
    Create feature branch (feature/aop)
      ↓
    Write code on feature branch
      ↓
    Push feature branch to GitHub
      ↓
    Create PR on GitHub (feature/aop → main)
      ↓
    Team reviews code
      ↓
    Approve and merge to main

- PR URL format: https://github.com/username/repo/pull/1
- Assignment only requires creating PR, not merging

---

## 6. Key Insights
- AOP intercepts Service methods, not Controller methods (based on Pointcut)
- Controller calls Service → AOP triggers at Service layer
- proceed() in @Around is mandatory to execute original method
- @After always runs, @AfterReturning only on success, @AfterThrowing only on error
- Interface injection enables loose coupling and easy implementation swapping
- DTO is just a simple data class, Repository is just an empty interface
- Spring identifies handler by HTTP method + URL combination, not method name
- PR = code review request, does not automatically merge