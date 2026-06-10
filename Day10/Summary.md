# Day10 - LLM Interaction Summary
**Date:** June 8, 2026
**Topics:** Spring IoC/DI, Bean Lifecycle, Bean Scope, Spring Annotations, HW7 Completion

---

## 1. Spring IoC and DI

### IoC (Inversion of Control)
- Transfers control of object creation from developer to Spring Container
- Instead of manually new-ing objects, Spring manages them
- Benefits: loose coupling, easier testing, easier to swap implementations

### IoC Container
- Core of Spring that manages Bean lifecycle
- BeanFactory: basic interface, lazy loading
- ApplicationContext: enhanced version, eager loading, used in practice
- Spring Boot uses AnnotationConfigWebApplicationContext internally

### BeanFactory vs ApplicationContext
- BeanFactory: basic CRUD for beans, lazy initialization
- ApplicationContext: extends BeanFactory, adds AOP, events, i18n, eager init
- Always use ApplicationContext in practice

### Dependency Injection
- Mechanism Spring uses to implement IoC
- Class declares what it needs, Spring provides it at runtime using reflection

### Three Types of DI

| Type | Recommended | Pros | Cons |
|------|-------------|------|------|
| Constructor | Most recommended | Mandatory deps, supports final, easy to test | Too many params = code smell |
| Setter | Optional deps | Flexible, changeable after creation | Object may be incomplete |
| Field | Not recommended | Simple to write | Uses reflection, hard to test |

---

## 2. Spring Annotations

### Component Registration
- @Component: generic Bean, auto-detected by component scan
- @Service: business logic layer
- @Repository: data access layer, translates DB exceptions
- @Controller: presentation layer, returns view name
- @RestController: = @Controller + @ResponseBody, returns JSON

### Configuration
- @Configuration: marks class as Bean definition source (like XML config)
- @ComponentScan: tells Spring which packages to scan for Beans
- @Bean: method-level, manually defines Bean (for third-party classes)

### @Component vs @Bean
- @Component: used on your own classes, auto-registered
- @Bean: used inside @Configuration, manually configured
- Use @Bean when you need to configure third-party classes you cannot modify

---

## 3. Bean Lifecycle

### Full Lifecycle Steps
1. Instantiation → Spring creates object via reflection
2. Populate Properties → inject dependencies (@Autowired)
3. BeanNameAware/BeanFactoryAware → inform Bean of its name/factory
4. Pre-initialization BeanPostProcessors → AOP applied here
5. InitializingBean's afterPropertiesSet() → initialization
6. Custom init-method / @PostConstruct → custom initialization
7. Post-initialization BeanPostProcessors
8. Bean is Ready To Use
9. DisposableBean's destroy() / @PreDestroy → cleanup on shutdown
10. Custom destroy-method

### Key Annotations for Lifecycle
- @PostConstruct → runs after Bean initialized (load cache, open connections)
- @PreDestroy → runs before Bean destroyed (close connections, release resources)

### Interview Simplified Version
    Instantiation → Inject Dependencies → Init → Ready → Destroy

---

## 4. Bean Scope

| Scope | Instances | Lifecycle | Use Case |
|-------|-----------|-----------|---------|
| Singleton | 1 (default) | Entire application | Service, Repository, Config |
| Prototype | New each time | Per request | Shopping cart, Form object, Report generator |
| Request | 1 per HTTP request | Single request | Request metadata, Validation results |
| Session | 1 per user session | User session | User profile, Preferences, Multi-step form |
| Application | 1 | Entire web app | Global config, Shared cache |

### Singleton vs Prototype
- Singleton: stateless, shared, created once
- Prototype: stateful, new instance every time, not managed after creation

---

## 5. Other Concepts

### Session vs Cookie
- Cookie: stored on client (browser), sent with every request, less secure
- Session: stored on server, identified by session ID in cookie, more secure
- Cookie can persist after browser closes, session typically ends with browser

### IOC Container Hierarchy
    BeanFactory (interface)
    ├── XmlBeanFactory (deprecated)
    └── ApplicationContext (interface)
        ├── ClassPathXmlApplicationContext
        ├── FileSystemXmlApplicationContext
        └── AnnotationConfigWebApplicationContext ← Spring Boot uses this

---

## 6. HW7 Completed Topics

- Singleton pattern with double-checked locking
- Reflection: runtime class inspection and manipulation
- HTTP status codes (200/201/202/204/301/307/400/401/403/404/500)
- HTTP protocol and request/response structure
- HTTP methods (GET/POST/PUT/DELETE/PATCH)
- POST vs PATCH, POST vs PUT
- Idempotent methods (GET, PUT, DELETE)
- TCP 3-way handshake
- TCP vs UDP
- Tomcat and its components
- Web server concept
- 3-tier architecture
- OSI model 7 layers
- OOM causes, diagnosis, and prevention

---

## 7. Key Insights
- @RestController = @Controller + @ResponseBody
- @Component/@Service/@Repository/@Controller are all the same internally
- Constructor injection recommended: mandatory deps, supports final, testable
- Field injection not recommended: reflection overhead, hard to test
- Bean lifecycle: most important to know @PostConstruct and @PreDestroy
- Singleton is default and most commonly used scope
- Session stores data server-side (secure), Cookie stores client-side (less secure)
- ApplicationContext extends BeanFactory with more enterprise features