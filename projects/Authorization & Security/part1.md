# Module: Security & Middleware in FastAPI

# Lesson 1 — Middleware in FastAPI

---

# What is Middleware?

Middleware is a piece of code that executes **before** the request reaches the API endpoint and **after** the endpoint returns a response.

It sits between the client and your route handler.

```
                Incoming Request
                      │
                      ▼
              ┌────────────────┐
              │   Middleware   │
              └────────────────┘
                      │
                      ▼
              ┌────────────────┐
              │ Authentication │
              └────────────────┘
                      │
                      ▼
              ┌────────────────┐
              │ API Endpoint   │
              └────────────────┘
                      │
                      ▼
              Database / Service
                      │
                      ▼
              API Endpoint Response
                      │
                      ▼
              ┌────────────────┐
              │   Middleware   │
              └────────────────┘
                      │
                      ▼
                 Client Response
```

---

# Why do we use Middleware?

Imagine you own a shopping mall.

Before a customer enters:

* Security guard checks the bag.
* Metal detector scans the customer.
* Visitor details are recorded.

After shopping:

* Bill is checked.
* Exit gate opens.

The customer doesn't know these processes happen every time.

Middleware works in the same way.

---

# Real-world Uses of Middleware

In production FastAPI projects, middleware is commonly used for:

* Logging every request
* Authentication
* Authorization
* Measuring request execution time
* CORS handling
* Compression (GZip)
* Security headers
* Rate limiting
* Request ID generation
* Monitoring and analytics

---

# Request Lifecycle

When a client sends a request:

```
Client
   │
   ▼
Middleware (Before Request)
   │
   ▼
Dependency Injection
   │
   ▼
API Route
   │
   ▼
Business Logic
   │
   ▼
Database
   │
   ▼
Business Logic
   │
   ▼
API Route
   │
   ▼
Middleware (After Response)
   │
   ▼
Client
```

---

# Types of Middleware in FastAPI

### 1. Built-in Middleware

Provided by FastAPI/Starlette.

Examples:

* CORSMiddleware
* GZipMiddleware
* HTTPSRedirectMiddleware
* TrustedHostMiddleware
* SessionMiddleware

---

### 2. Custom Middleware

Written by developers to add application-specific behavior.

Examples:

* Logging
* Request timing
* Request ID
* API key validation
* Security headers
* Custom analytics

---

# Execution Order

If multiple middleware are added, the last one added runs first on the request and last on the response.

Example:

```python
app.add_middleware(MiddlewareA)
app.add_middleware(MiddlewareB)
app.add_middleware(MiddlewareC)
```

### Request flow

```
Client
  │
  ▼
Middleware C
  ▼
Middleware B
  ▼
Middleware A
  ▼
Route
```

### Response flow

```
Route
  ▼
Middleware A
  ▼
Middleware B
  ▼
Middleware C
  ▼
Client
```

This "wrapper" behavior is important when combining authentication, logging, CORS, and security middleware.

---

# Middleware Function Signature

A custom middleware created with `@app.middleware("http")` always receives:

```python
request: Request
call_next
```

* `request`: contains the incoming HTTP request.
* `call_next`: passes the request to the next middleware or route handler and returns the response.

---

# Common Workflow

1. Client sends a request.
2. Middleware receives the request.
3. Middleware performs its task (e.g., logging or timing).
4. Middleware calls `call_next(request)`.
5. The request reaches the API endpoint.
6. The endpoint returns a response.
7. Middleware can modify the response.
8. The response is sent back to the client.

---

**Next Lesson:** We'll write our first custom middleware—**Logging Middleware**—that logs every incoming request and outgoing response.
