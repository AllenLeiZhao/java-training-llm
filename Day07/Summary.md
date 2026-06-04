# Day07 - LLM Interaction Summary
**Date:** June 3, 2026
**Topics:** Web Basics, HTTP, RESTful API, Architecture, Mock Practice

---

## 1. Web Architecture Concepts

### Client-Server Model
- Client (browser/mobile app) sends HTTP request to server
- Server handles business logic and database operations, returns HTTP response
- One server handles multiple clients simultaneously via multi-threading

### Application Service
- Software that hosts and runs business logic of a web application
- Common tools: Tomcat, JBoss, Jetty
- Spring Boot embeds Tomcat by default

### Horizontal vs Vertical Scaling
- Horizontal: add more servers, theoretically unlimited, supports load balancing
- Vertical: upgrade single server (CPU/RAM), has physical limit, more expensive
- Horizontal scaling recommended in modern architecture

### Load Balancer
- Sits between client and servers
- Evenly distributes requests to multiple servers
- Prevents single server from being overwhelmed
- Improves high availability: redirects traffic when a server goes down

### Microservice vs Microfrontend
- Microservice: splits application into small independent services, each deployable separately
- Microfrontend: same concept applied to frontend UI
- Benefits: independent deployment, no single point of failure, better maintainability

### API Gateway vs Load Balancer
- API Gateway: single entry point, handles authentication, rate limiting, routing → comes FIRST
- Load Balancer: distributes traffic across server instances → comes AFTER API Gateway
- API Gateway = security guard, Load Balancer = traffic distributor

### Message Queue
- Asynchronous communication tool between backend services
- Decouples sender and receiver
- Sender leaves message and continues immediately without waiting
- Handles peak traffic by storing messages safely
- Common tools: Kafka, RabbitMQ
- Sits between services, not in client-server request flow

### Log vs Monitor
- Log: records system activities (requests, errors, operations) for troubleshooting/auditing
- Monitor: tracks real-time metrics (CPU, error rate, response time), triggers alerts
- Log = after incident, Monitor = real-time prevention
- Tools: ELK Stack (log), Prometheus + Grafana, AWS CloudWatch (monitor)

### Security
- Authentication: verifies WHO you are (username/password, JWT token)
- Authorization: verifies WHAT you can do (role-based access)
- "Authentication asks who are you, Authorization asks what can you do"

---

## 2. HTTP & RESTful API

### HTTP Request Components
- Method: GET, POST, PUT, DELETE, PATCH
- URL
- Request Header (Content-Type, Authorization)
- Request Body (optional: POST/PUT have body, GET/DELETE typically don't)

### HTTP Response Components
- Status code (200 OK, 201 Created, 404 Not Found, 500 Server Error)
- Response Header
- Response Body (usually JSON)

### HTTP Status Codes
- 1xx: Informational
- 2xx: Success (200, 201, 204)
- 3xx: Redirection (301, 302)
- 4xx: Client Error (400, 401, 403, 404)
- 5xx: Server Error (500, 502, 503)

### HTTP Methods

| Method | Safe | Idempotent |
|--------|------|------------|
| GET | ✅ | ✅ |
| POST | ❌ | ❌ |
| PUT | ❌ | ✅ |
| DELETE | ❌ | ✅ |
| PATCH | ❌ | ❌ |

- Safe: does not alter server state
- Idempotent: multiple identical requests = same effect

### RESTful Design Rules
- Use nouns not verbs in URLs
- Use HTTP methods to express actions
- PathVariable for specific resource: `/books/{id}`
- RequestParam for filtering/sorting: `/books?author=Allen&page=1`

### SOAP vs RESTful
- SOAP: stateful, XML format, complex, legacy
- RESTful: stateless, JSON format, simple, modern standard

### PathVariable vs RequestParam
- PathVariable: identifies specific resource `/books/123` → `@PathVariable`
- RequestParam: filtering/search condition `/books?author=Allen` → `@RequestParam`

---

## 3. Network & Architecture

### OSI vs TCP/IP Model
- OSI: 7 layers (theoretical)
- TCP/IP: 4 layers (practical)
- Key layers: Application (HTTP), Transport (TCP/UDP), Network (IP)

### TCP vs UDP
- TCP: connection-based, reliable, slower (HTTP, file transfer)
- UDP: connectionless, unreliable, faster (video streaming, gaming)

### TCP Three-Way Handshake (connect)
    Client → SYN → Server
    Client ← SYN+ACK ← Server
    Client → ACK → Server

### TCP Four-Way Handshake (disconnect)
    Client → FIN → Server
    Client ← ACK ← Server
    Client ← FIN ← Server
    Client → ACK → Server

### Three-Tier Architecture
    Controller → handles HTTP requests
    Service → business logic
    Repository/DAO → database operations

---

## 4. Key Insights
- API Gateway comes BEFORE Load Balancer in request flow
- Message Queue sits BETWEEN services, not in client-server flow
- GET/DELETE have no request body; POST/PUT/PATCH have body
- OOM is an Error not Exception (extends Error, not Exception)
- 4xx = client's fault, 5xx = server's fault
- Authentication = who you are, Authorization = what you can do

---

## 5. Mock Practice Results (3 Rounds)
- Round 1: 96/100
- Round 2: 96.8/100
- Round 3: 98.5/100
- Consistent improvement across all rounds
- Strongest topics: HTTP, Scaling, Load Balancer, Security, Testing
- Areas to watch: grammar fluency, remembering tool names