# Day09 - LLM Interaction Summary
**Date:** June 5, 2026
**Topics:** Spring Boot Project, PostgreSQL, JPA, Hibernate, Lombok, CRUD API

---

## 1. Spring Boot Project Setup

### application.properties Configuration
- `spring.datasource.url` → database connection (jdbc:postgresql://localhost:5433/student_db)
- `spring.datasource.username/password` → database credentials
- `spring.jpa.hibernate.ddl-auto=update` → auto create/update tables, no data loss
- `spring.jpa.show-sql=true` → print SQL in console
- Port 5433 instead of 5432 due to Docker port mapping

### Project Package Structure
    controller  → handles HTTP requests
    dto         → data transfer objects
    entity      → database table mapping
    exception   → custom exceptions
    mapper      → converts between Entity and DTO
    repository  → database operations
    service     → business logic

---

## 2. Core Concepts

### Hibernate
- ORM framework (Object Relational Mapping)
- Automatically maps Java objects to database tables
- Developed by Red Hat, open source
- Automatically included via Spring Data JPA dependency

### Lombok
- Code generation tool, reduces boilerplate code
- Developed by two Dutch developers, open source
- Generates code at compile time, no runtime overhead

### Common Lombok Annotations

| Annotation | Purpose |
|------------|---------|
| @Getter | Generate all getters |
| @Setter | Generate all setters |
| @NoArgsConstructor | Generate no-args constructor (required by Hibernate) |
| @AllArgsConstructor | Generate all-args constructor |
| @Data | = @Getter + @Setter + @ToString + @EqualsAndHashCode |
| @RequiredArgsConstructor | Generate constructor for final fields only |

---

## 3. Entity Layer

### JPA Annotations
- `@Entity` → marks class as a database table
- `@Table(name = "students")` → specifies table name
- `@Id` → marks primary key
- `@GeneratedValue(strategy = GenerationType.IDENTITY)` → auto increment
- `@Column(nullable = false, unique = true)` → column constraints

### Why Long not long
- Entity primary key should use wrapper class (Long)
- Database fields can be null, primitive long cannot
- New object before saving has null id

### Why not use Record as Entity
- Hibernate requires no-args constructor
- Hibernate requires mutable objects (setters)
- Record is immutable and final, Hibernate cannot proxy it

---

## 4. Repository Layer

### JpaRepository<Student, Long>
- Student → Entity type to manage
- Long → primary key type
- Automatically provides all CRUD methods: save, findById, findAll, deleteById, count, existsById
- No implementation code needed

### Inheritance Hierarchy
    JpaRepository
    └── PagingAndSortingRepository
        └── CrudRepository
            └── Repository

---

## 5. DTO and Mapper

### Why DTO
- Controls which fields are returned to client
- Hides sensitive information
- Decouples database layer from API layer

### Mapper (Static Methods)
- `mapToStudentDTO()` → Entity to DTO
- `mapToStudent()` → DTO to Entity
- Pure static methods, no Spring injection needed

---

## 6. Service Layer

### Why Interface + Impl Pattern
- Controller depends on Interface, not concrete implementation
- Decoupled, easier to test and mock
- Can have multiple implementations

### 5 Business Methods
- createStudent: DTO → Entity → save → DTO
- getStudentById: findById → orElseThrow → DTO
- getAllStudents: findAll → stream → map → toList
- updateStudent: findById → update fields → save → DTO
- deleteStudent: deleteById

### Key Annotations
- `@Service` → register as Spring Bean
- `@RequiredArgsConstructor` → constructor injection for Repository

---

## 7. Controller Layer

### Annotations
- `@RestController` = @Controller + @ResponseBody → returns JSON
- `@RequestMapping("/api/students")` → base URL path
- `@RequiredArgsConstructor` → constructor injection for Service

### 5 API Endpoints

| Method | URL | Status Code |
|--------|-----|-------------|
| POST | /api/students | 201 Created |
| GET | /api/students | 200 OK |
| GET | /api/students/{id} | 200 OK |
| PUT | /api/students/{id} | 200 OK |
| DELETE | /api/students/{id} | 200 OK |

### ResponseEntity
- Wraps return value with HTTP status code
- `new ResponseEntity<>(body, HttpStatus.CREATED)` → 201
- `ResponseEntity.ok(body)` → 200

---

## 8. Complete Request Flow

    Postman
      ↓ HTTP Request
    Controller (receives request, calls Service)
      ↓
    Service (business logic, calls Repository)
      ↓
    Repository (database operations)
      ↓
    PostgreSQL (stores/retrieves data)
      ↑
    Mapper (Entity ↔ DTO conversion)
      ↑
    Controller (returns HTTP Response)
      ↑
    Postman

---

## 9. Postman Test Results
- POST create student → 201 Created ✅
- GET all students → 200 OK ✅
- GET student by id → 200 OK ✅
- PUT update student → 200 OK ✅
- DELETE student → 200 OK ✅
- Duplicate email → 500 (should be 400, needs GlobalExceptionHandler)

---

## 10. Key Insights
- Docker port mapping: external 5433 → internal 5432
- Nested git repositories cause issues, Spring Boot project should be separate
- `ddl-auto=update` → updates only on changes, no data loss, recommended for development
- Entity fields must be private with getter/setter (Hibernate uses reflection)
- JpaRepository provides all CRUD methods, no SQL needed
- Service Interface + Impl pattern: decoupled, easier to test
- 500 errors should be converted to 4xx using GlobalExceptionHandler
- Lombok @Data is shorthand for @Getter + @Setter + @ToString + @EqualsAndHashCode
- Record cannot be used as Entity due to immutability and Hibernate requirements