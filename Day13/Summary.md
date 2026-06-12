# Day13 - LLM Interaction Summary
**Date:** June 11, 2026
**Topics:** Spring Boot Deep Dive, IOC/DI, AOP, Security/JWT, Profile, Transactional, WebFlux, Bean Scope, Eureka, CQRS, Actuator, Testing, Mock Interview Practice

---

## 1. Core Concepts Reviewed

### IOC (Inversion of Control)
- Instead of creating and managing objects yourself, hand over control to Spring IOC container
- Container creates and manages all beans, injects them where needed
- Benefit: in large applications with hundreds of classes and complex relationships, IOC handles wiring automatically → loose coupling, easier to test
- IOC is the concept; DI (Dependency Injection) is how IOC is implemented

### DI (Dependency Injection) — Three Ways
1. **Constructor Injection** (recommended)
   - Dependencies required at object creation time → always in valid state
   - Prevents NullPointerException at runtime
   - Easiest to unit test (pass mock objects directly)
2. **Setter Injection** (rarely used)
   - Dependencies optional → can lead to incomplete object state
3. **Field Injection** (not recommended)
   - @Autowired directly on field → convenient but hard to unit test

### Bean Registration Annotations
- @Component, @Service, @Repository, @Controller, @RestController: placed on class, auto-scanned
- @Bean: placed on method inside @Configuration class
- Key use case for @Bean: third-party classes whose source code cannot be modified

### By-Type vs By-Name Injection
- Spring tries by-type first → if exactly one match, inject directly
- Multiple beans of same type → falls back to by-name (variable name matches bean name)
- @Primary: mark one bean as default choice (global preference)
- @Qualifier: specify exact bean name at injection point (precise control)

### Bean Scope (5 types)
- **Singleton** (default): one instance per Spring IOC container; for stateless components
- **Prototype**: new instance every time requested/injected; Spring does NOT manage destruction
- **Request**: one instance per HTTP request; web applications only
- **Session**: one instance per user session; destroyed when session ends
- **Application**: one instance per ServletContext; similar to singleton but at ServletContext level
- Singleton vs Application: singleton = IOC container level; application = ServletContext level (in practice almost the same)

---

## 2. AOP Deep Dive

### Core Concepts
- Aspect: class annotated with @Aspect containing cross-cutting logic
- Pointcut: defines WHERE to intercept (which classes/methods)
- JoinPoint: the specific method execution being intercepted at runtime
- Advice: the action to execute (Before/After/AfterReturning/AfterThrowing/Around)
- Proxy: Spring wraps bean in proxy; proxy intercepts method calls

### Five Advice Types
- @Before: runs before the method
- @After: runs after regardless of outcome
- @AfterReturning: runs only on successful return
- @AfterThrowing: runs only when exception thrown
- @Around: wraps entire method; must call proceed() to execute original; most powerful

### Critical Limitations
- **Cannot apply to private methods**: proxy only intercepts public method calls from outside
- **this. bypasses proxy**: even public methods called via this. inside same class skip AOP
- Must call through Spring-managed proxy for AOP to trigger
- AspectJ (compile-time weaving) can intercept private methods but Spring AOP cannot

### Custom Annotation with AOP
- Define with @interface keyword
- Meta-annotations: @Target (where it can be used), @Retention(Runtime) (available via reflection), @Documented
- Define Pointcut in Aspect class targeting the custom annotation
- Example: @LogExecutionTime annotation triggers @Around advice automatically

### Project Usage
- @Around advice on all service layer methods
- Logs: method name, input arguments, execution time, return value, exceptions
- No logging code needed inside service methods themselves

---

## 3. Exception Handling + Validation

### Global Exception Handling
- @RestControllerAdvice = @ControllerAdvice + @ResponseBody (auto-serializes to JSON)
- @ExceptionHandler on each method specifies which exception type it handles
- Flow: exception thrown anywhere → Spring routes to matching @ExceptionHandler → returns ResponseEntity with status code + error message
- Benefits: controllers stay clean, consistent error format across application

### @ControllerAdvice vs @RestControllerAdvice
- @ControllerAdvice: general purpose, must add @ResponseBody manually for JSON
- @RestControllerAdvice: @ResponseBody applied automatically to all handler methods
- Same relationship as @Controller vs @RestController

### ResponseEntity vs @ResponseBody
- @ResponseBody: controls return content only, status code defaults to 200
- ResponseEntity: controls body + HTTP status code + response headers
- Use ResponseEntity when you need precise control over status codes (404, 400, 201, etc.)

### Bean Validation
- Add constraint annotations to DTO/Entity fields: @NotNull, @NotBlank, @Email, @Min, @Max, @Size, @Pattern
- Add @Valid to controller method parameter to trigger validation
- Failure throws MethodArgumentNotValidException → caught by @RestControllerAdvice → returns 400
- MethodArgumentNotValidException = Method + Argument + NotValid + Exception (name describes the problem)

---

## 4. Spring Security + JWT

### Two Phases
**Phase 1 — Login (happens once):**
1. Client sends username + password to /login
2. Spring Security's AuthenticationManager validates credentials
3. JWT Utility class generates token
4. Token returned to client in response body
5. Client stores token in localStorage or cookie

**Phase 2 — Subsequent requests:**
1. Client sends request with token in Authorization header
2. JWT Filter intercepts request, validates token signature and expiry
3. Extracts user identity and role from token, stores in Security Context
4. Spring Security checks if role has permission for requested endpoint
5. Valid + authorized → request reaches Controller
6. Invalid → 401 Unauthorized; valid but wrong role → 403 Forbidden

### Authentication vs Authorization
- Authentication: verify who you are (username + password → JWT token)
- Authorization: verify what you are allowed to do (roles → endpoint access)
- Authentication always happens first

### API Gateway vs Spring Security Authentication
- API Gateway (coarse-grained): does token exist? is it expired? correct format?
- Spring Security (fine-grained): valid signature? who is the user? what is their role? permission for this specific endpoint?
- API Gateway = building security guard (checks if you have a badge)
- Spring Security = floor-by-floor access control (checks what areas you can enter)

### Role-Based Authorization Implementation
- Security configuration class: define URL access rules by role
- @PreAuthorize on controller methods: fine-grained method-level control
- 401 = not authenticated (no valid token); 403 = authenticated but not authorized (wrong role)

---

## 5. Spring Profiles

### What is a Profile
- Mechanism to define different configurations for different environments (dev, test, prod)
- Separate property files: application.properties (shared) + application-dev.properties + application-prod.properties
- Switch environments without changing code

### Four Ways to Activate a Profile
1. **application.properties**: set spring.profiles.active=dev (local default, must manually change)
2. **JVM argument**: --spring.profiles.active=prod at startup (IntelliJ Run Config or command line jar)
3. **Environment variable**: SPRING_PROFILES_ACTIVE=prod on server (most secure for production, config separated from code)
4. **@ActiveProfiles annotation**: on test classes only (tests run automatically, can't pass startup args)

### When to Use Each
- application.properties → local development default
- JVM argument → temporary local switch or server jar startup
- Environment variable → production (safest, keeps secrets out of code)
- @ActiveProfiles → unit/integration tests

---

## 6. @Transactional

### What It Does
- Ensures atomicity: all database operations in the method succeed together or all roll back
- Typically added to service layer methods (multiple DB operations as one business unit)
- Internally implemented through AOP (proxy wraps method, opens/commits/rolls back transaction)

### Default Rollback Behavior
- **Only rolls back on RuntimeException by default**
- Checked exceptions do NOT trigger rollback unless explicitly configured with rollbackFor attribute

### Class Level vs Method Level
- Class level: all public methods in the class get transactional behavior
- Method level: only that specific method has a transaction
- Can combine: class-level default + method-level override for specific settings

### Transaction Propagation
- **REQUIRED** (default): second method joins existing transaction → one failure rolls back everything
- **REQUIRES_NEW**: second method starts independent transaction → failures isolated from each other

---

## 7. WebFlux / Async

### Spring MVC vs WebFlux
- Spring MVC: synchronous + blocking; each request holds a thread until complete; thread sits idle waiting for DB/API response
- WebFlux: non-blocking + reactive; threads don't wait; handle other requests while waiting; continue when data arrives
- WebFlux uses Mono (0 or 1 value) and Flux (0 to many values stream)
- Choose WebFlux for: high concurrency, real-time data streaming
- Spring MVC sufficient for: standard business applications

### @Async
- Add @EnableAsync to configuration class
- Add @Async to method → runs in separate thread from managed thread pool
- Returns void or CompletableFuture
- Use case: background tasks like sending emails, push notifications

### Relationship Between Async Concepts
    Future → can do other things but get() still blocks
        ↓
    CompletableFuture → truly non-blocking, auto callback on completion
        ↓
    WebFlux Mono/Flux → framework-level non-blocking end to end
        ↓
    Message Queue → decouples services, sender doesn't wait for receiver

### Spring MVC Flow (Complete)
    Client
      ↓ API Gateway (routing, coarse auth, rate limiting, SSL termination, logging)
      ↓ Load Balancer (distributes to server instances)
      ↓ Spring Security Filter (validates JWT, checks authorization)
      ↓ DispatcherServlet (front controller, routes to correct controller method)
      ↓ Controller → Service → Repository
      ↓ ResponseEntity (@RestController auto-serializes to JSON)
      ↓ HTTP Response to Client

### API Gateway Responsibilities
- Routing: direct requests to correct microservice
- Coarse-grained authentication: check token exists and not expired
- Rate limiting: prevent overwhelming backend (429 Too Many Requests)
- SSL termination: handle HTTPS encryption/decryption at edge
- Centralized logging and monitoring
- Sometimes caching
- Note: Load Balancer is a separate component; some API Gateway products (Spring Cloud Gateway) have built-in load balancing but they are conceptually distinct

---

## 8. Spring Cloud / Eureka

### Why Eureka
- In cloud environments IP addresses change constantly as services scale
- Services need to find each other by name, not hardcoded IP

### Eureka Roles
- **Eureka Server**: the registry (phone book), stores all service info; @EnableEurekaServer
- **Eureka Client**: each microservice; registers itself by name on startup; @EnableEurekaClient
- **Heartbeat**: each client sends periodic heartbeat; server removes service if heartbeat stops

### Configuration
Eureka Server application.properties:
- server.port=8761
- eureka.client.register-with-eureka=false (server doesn't register with itself)
- eureka.client.fetch-registry=false

Eureka Client application.properties:
- spring.application.name=order-service (name used for registration and lookup)
- eureka.client.service-url.defaultZone=http://localhost:8761/eureka

### Netflix Connection
- Eureka was developed and open-sourced by Netflix as part of Netflix OSS
- Spring Cloud integrated Netflix OSS tools into the Spring ecosystem
- Netflix OSS also includes: Ribbon (load balancing), Hystrix (circuit breaker), Zuul (API gateway)
- Netflix stopped maintaining Hystrix and Ribbon; Spring replaced them with Spring Cloud LoadBalancer and Resilience4j
- Eureka still actively used; alternatives: Consul, Zookeeper

---

## 9. CQRS Pattern

### What It Is
- Command Query Responsibility Segregation
- Command = write operations (create, update, delete) → optimize for consistency and business logic
- Query = read operations → optimize for speed and performance
- Separate the two so each can be optimized independently

### Why Use It
- Most applications have far more reads than writes
- Read side: speed, caching, read replicas
- Write side: consistency, transactions, complex business rules
- Traditional architecture mixes both → hard to optimize either side

### Practical Application
- Full CQRS: separate models, separate databases, event sourcing
- Lightweight CQRS (more common): separate read/write methods in service layer, different DTOs for reads and writes
- Remember: Command=write, Query=read (not the other way around)

---

## 10. Testing

### Frameworks Used
- **JUnit 5**: test framework, defines test methods with @Test
- **Mockito**: mock dependencies; @Mock creates mock, @InjectMocks injects into class under test
- **MockMvc**: simulates HTTP requests to test controller layer without starting real server (like automated Postman)

### Test Types
- **Unit Test**: test one class; mock all dependencies; @ExtendWith(MockitoExtension.class); fast
- **Integration Test**: load full Spring context; @SpringBootTest; slow
- **Controller Test**: @WebMvcTest loads only controller layer; use MockMvc to send requests and verify response status + body

### MockMvc vs Postman
- Both simulate HTTP requests and check responses
- Postman: manual, requires running server, not automated
- MockMvc: automated, runs inside test code, no real server needed, runs in CI/CD pipeline

---

## 11. Mock Interview Practice — Common Mistakes

### Pronunciation / Terminology Errors
- **logging** (日志, LAW-ging) vs **login** (登录, LOG-in) — critical to distinguish
- **routed** not "rooted"
- **DispatcherServlet** not "Dispatcher Handler"
- **Pointcut** not "cut point"
- **@Valid** not "validate annotation"
- **JUnit** not "Unit J"
- **response** not "responsibility"
- **logs in** not "locks in"

### Answer Structure Issues
- Always lead with conclusion sentence before details
- For open-ended questions: start with "It depends"
- When saying "I do something" → specify which layer (Controller receives, Service handles, Repository saves, Filter intercepts)
- Always explain WHY after stating WHAT

### Content Gaps Fixed
- Bean Scope: define what scope IS before listing the five types
- Debug: must start from logs, not from debugger
- CQRS: Command=write, Query=read (not reversed)
- @Transactional: only RuntimeException triggers rollback by default
- Eureka Client: it IS each microservice, not the registry itself

---

## 12. Key Insights
- IOC is the concept; DI is the implementation
- Constructor injection preferred: explicit, immutable, testable
- @Bean for third-party classes (can't add @Component to source you don't own)
- Prototype scope: Spring does not manage destruction (unlike all other scopes)
- JWT two phases: login (AuthenticationManager + JWT Utility) vs subsequent requests (JWT Filter)
- 401 = not authenticated; 403 = authenticated but not authorized
- API Gateway = coarse-grained auth; Spring Security = fine-grained auth
- Profile env variable = most secure for production (config separated from code)
- @Transactional default: REQUIRED propagation, rollback on RuntimeException only
- Eureka = Netflix OSS integrated into Spring Cloud; identifies services by name not IP
- CQRS: Command=write, Query=read; separate to optimize independently
- MockMvc = automated Postman that runs inside test code without a real server