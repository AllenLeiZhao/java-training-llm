# Day08 - LLM Interaction Summary
**Date:** June 4, 2026
**Topics:** Web Basics Review, Spring IoC/DI, Bean Scope, HTTP Annotations

---

## 1. Web Architecture Review

### HTTP Status Codes
- 1xx: Informational
- 2xx: Success (200 OK, 201 Created, 204 No Content)
- 3xx: Redirection (301 Permanent, 302 Temporary)
- 4xx: Client Error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found)
- 5xx: Server Error (500 Internal Server Error)
- 4xx = client's fault, 5xx = server's fault

### Web Server & Servlet
- Web Server (Tomcat, JBoss, Jetty) receives HTTP requests
- Servlet: Java class that handles specific URL patterns
- Each URL maps to a corresponding Servlet
- Modern Spring Boot replaces Servlet with @Controller/@RestController

### RESTful API Design
- Use nouns not verbs in URLs
- Use HTTP methods to express actions
- PathVariable: identifies specific resource `/books/{id}` → `@PathVariable`
- RequestParam: filtering/sorting condition `/books?author=Allen` → `@RequestParam`
- Both are valid in RESTful, serve different purposes

### API Gateway vs Load Balancer
- API Gateway: authentication, rate limiting, routing → comes FIRST
- Load Balancer: distributes traffic across instances of SAME service → comes AFTER
- Load Balancer only distributes within same service, not between different services

---

## 2. Spring IoC & DI

### IoC (Inversion of Control)
- Transfers object management from developer to Spring Container
- Instead of manually creating dependencies, Spring manages them
- Benefits: loose coupling, easier testing, easier to swap implementations

### Bean
- Any Java object managed by Spring Container
- Created, configured and managed by Spring
- Common annotations: @Component, @Service, @Repository, @Controller

### DI (Dependency Injection)
- How IoC is implemented
- Spring injects Beans into objects that need them
- Three types: Constructor, Setter, Field injection

### Three DI Methods

| | Constructor | Setter | Field |
|---|---|---|---|
| Recommended | ✅ Most | 🟡 Optional deps | ❌ Not recommended |
| Dependencies | Mandatory | Optional | Optional |
| final keyword | ✅ Can use | ❌ | ❌ |
| Testability | Easy | Medium | Difficult |

### Constructor Injection (Recommended)
    public class ProductService {
        private final ProductRepository productRepository;

        public ProductService(ProductRepository productRepository) {
            this.productRepository = productRepository;
        }
    }
- No @Autowired needed, Spring auto-detects
- Use final for immutability
- Lombok @RequiredArgsConstructor simplifies further

### Field Injection (Not Recommended)
    @Autowired
    private ProductRepository productRepository;
- Uses reflection → performance overhead
- Hard to test
- Dependencies not explicit

---

## 3. Bean Scope

| Scope | Instances | Lifecycle | Use Case |
|-------|-----------|-----------|---------|
| Singleton | 1 (default) | Entire application | Service, DAO |
| Prototype | New each time | Per request | Stateful objects |
| Request | 1 per HTTP request | Single request | Request temp data |
| Session | 1 per user session | User session | User info |
| Application | 1 | Entire web app | Global config |

- Singleton is default, most commonly used
- Scope is managed internally by Spring, not visible in business logic code

---

## 4. Spring Annotations

### @RestController
- `@RestController` = `@Controller` + `@ResponseBody`
- @Controller: marks class as HTTP request handler
- @ResponseBody: returns data directly as JSON (not view name)
- Use @RestController for RESTful APIs, @Controller for traditional MVC

### HTTP Method Annotations
    @GetMapping("/users")        // GET
    @PostMapping("/users")       // POST
    @PutMapping("/users/{id}")   // PUT
    @DeleteMapping("/users/{id}") // DELETE
    @PatchMapping("/users/{id}") // PATCH
- Annotations are labels, not statements → no semicolon needed

---

## 5. Spring Background
- Created by Rod Johnson in 2002
- Open source (Apache 2.0 license)
- Currently maintained by Broadcom (acquired VMware in 2023)
- VMware monetizes through enterprise support, Tanzu platform
- Open source = best marketing strategy

---

## 6. Key Insights
- IoC = Spring manages objects, Bean = managed object, DI = injection mechanism
- Constructor injection recommended: mandatory deps, supports final, easy to test
- Singleton scope is default, most beans should be singleton (stateless)
- @RestController returns JSON, @Controller returns view name
- Annotations have no semicolon, they are labels not statements
- Load Balancer distributes within SAME service instances only