# Module: Security & Middleware

# Lesson 8 — Rate Limiting in FastAPI (SlowAPI)

Rate limiting is one of the most important security mechanisms in any production API. It protects your application from abuse, brute-force attacks, bots, and denial-of-service attempts by restricting how many requests a client can make within a given time.

---

# Learning Objectives

By the end of this lesson, you'll understand:

* What rate limiting is
* Why it is needed
* Different types of rate limiting
* Installing and configuring `slowapi`
* Applying rate limits to endpoints
* Handling `429 Too Many Requests`
* Production best practices

---

# What is Rate Limiting?

Rate limiting controls the number of requests a client can make during a specified time window.

Example:

```text
Limit: 5 requests/minute

Request 1   ✅ Allowed
Request 2   ✅ Allowed
Request 3   ✅ Allowed
Request 4   ✅ Allowed
Request 5   ✅ Allowed
Request 6   ❌ Blocked (429 Too Many Requests)
```

---

# Why Do We Need Rate Limiting?

Imagine your login API:

```http
POST /login
```

An attacker tries thousands of passwords.

```text
password
password123
admin
admin123
welcome
qwerty
123456
...
```

Without rate limiting:

```text
Attacker
     │
     ▼
100,000 Login Requests
     │
     ▼
Server Overloaded
```

With rate limiting:

```text
Attacker
     │
     ▼
5 Requests / Minute
     │
     ▼
429 Too Many Requests
```

---

# Real-World Examples

| API              | Typical Limit        |
| ---------------- | -------------------- |
| Login API        | 5 requests/minute    |
| OTP Verification | 3 requests/5 minutes |
| Search API       | 100 requests/minute  |
| Public API       | 1000 requests/hour   |
| Password Reset   | 2 requests/hour      |

Different endpoints often have different limits based on their sensitivity.

---

# Types of Rate Limiting

## 1. IP-Based

```text
192.168.1.10

↓

100 requests/minute
```

Most common and simple to implement.

---

## 2. User-Based

```text
User ID: 1001

↓

1000 requests/hour
```

Useful after authentication.

---

## 3. API Key-Based

```text
API Key

↓

5000 requests/day
```

Common for public APIs.

---

## 4. Endpoint-Based

```text
/login

↓

5 requests/minute

-----------------------

/courses

↓

100 requests/minute
```

Different endpoints receive different protection levels.

---

# Installing SlowAPI

Install the required packages:

```bash
pip install slowapi limits
```

---

# Project Structure

```text
app/
│
├── core/
│   └── limiter.py
│
├── main.py
│
└── api/
    └── routes.py
```

---

# Step 1 — Create Limiter

## File: `app/core/limiter.py`

```python
from slowapi import Limiter
from slowapi.util import get_remote_address

limiter = Limiter(
    key_func=get_remote_address
)
```

### Explanation

* `Limiter` manages request limits.
* `get_remote_address` identifies the client by IP address.

---

# Step 2 — Configure FastAPI

## Update `app/main.py`

```python
from fastapi import FastAPI

from slowapi.errors import RateLimitExceeded
from slowapi.middleware import SlowAPIMiddleware
from slowapi import _rate_limit_exceeded_handler

from app.core.limiter import limiter

app = FastAPI()

app.state.limiter = limiter

app.add_exception_handler(
    RateLimitExceeded,
    _rate_limit_exceeded_handler
)

app.add_middleware(SlowAPIMiddleware)
```

### Explanation

* `app.state.limiter` stores the shared limiter instance.
* `SlowAPIMiddleware` checks request limits.
* The exception handler returns a proper `429 Too Many Requests` response.

---

# Step 3 — Apply Rate Limits

```python
from fastapi import APIRouter, Request

from app.core.limiter import limiter

router = APIRouter()


@router.get("/courses")
@limiter.limit("10/minute")
async def get_courses(request: Request):
    return {
        "courses": [
            "Python",
            "FastAPI",
            "React"
        ]
    }
```

> **Important:** When using `slowapi`, the endpoint must include a `Request` parameter because the limiter inspects the incoming request.

---

# Multiple Examples

### Login API

```python
@router.post("/login")
@limiter.limit("5/minute")
async def login(request: Request):
    ...
```

---

### Search API

```python
@router.get("/search")
@limiter.limit("100/minute")
async def search(request: Request):
    ...
```

---

### OTP API

```python
@router.post("/otp")
@limiter.limit("3/5minutes")
async def otp(request: Request):
    ...
```

---

# Testing

Call:

```http
GET /courses
```

The first 10 requests within one minute:

```text
200 OK
```

The 11th request:

```http
429 Too Many Requests
```

Example response:

```json
{
    "error": "Rate limit exceeded: 10 per 1 minute"
}
```

---

# Response Headers

Many rate-limiting implementations also expose helpful headers such as:

```http
X-RateLimit-Limit: 10

X-RateLimit-Remaining: 3

Retry-After: 45
```

These indicate:

* Maximum allowed requests
* Remaining requests
* Seconds until the limit resets

---

# Development vs Production

### Development

```python
@limiter.limit("1000/minute")
```

Higher limits make testing easier.

---

### Production

```python
@limiter.limit("5/minute")
```

Stricter limits for sensitive endpoints like login.

---

# Common Mistakes

## ❌ Applying the same limit everywhere

```text
Login

5/minute

Search

5/minute
```

Search endpoints generally require much higher limits.

---

## ❌ Using only IP-based limits

Many users behind the same NAT or corporate network share one public IP. Consider user-based or API key-based limits after authentication.

---

## ❌ Forgetting reverse proxies

When deploying behind Nginx or a load balancer, configure trusted proxy headers correctly. Otherwise, every request may appear to come from the proxy's IP instead of the real client.

---

# Production Best Practices

### Small Applications

* In-memory rate limiting is usually sufficient.

### Distributed Applications

Use a shared backend such as **Redis** so all application instances enforce the same limits consistently.

Typical architecture:

```text
Client
   │
   ▼
Load Balancer
   │
   ├─────────────┐
   ▼             ▼
FastAPI 1    FastAPI 2
   │             │
   └──────┬──────┘
          ▼
        Redis
```

This avoids each server maintaining separate counters.

---

# Current Project Features

Your SkillHub API now includes:

* ✅ Logging Middleware
* ✅ Request Processing Time Middleware
* ✅ Request ID Middleware
* ✅ Security Headers Middleware
* ✅ CORS Configuration
* ✅ Security Best Practices
* ✅ Rate Limiting

---

# Interview Questions

### 1. What is rate limiting?

A technique that restricts the number of requests a client can make within a specified period.

---

### 2. Why is rate limiting important?

It helps prevent brute-force attacks, abuse, resource exhaustion, and denial-of-service attempts.

---

### 3. What does HTTP 429 mean?

It indicates **Too Many Requests**—the client has exceeded the configured rate limit.

---

### 4. Why is Redis preferred for rate limiting in production?

Redis provides a fast, centralized data store so multiple FastAPI instances can share rate-limit counters consistently.

---

### 5. Why does SlowAPI require a `Request` parameter?

Because it uses the request information (such as the client IP) to identify and track requests for rate limiting.

---

# Module Summary

You have completed the **Security & Middleware** module and implemented production-ready concepts commonly found in FastAPI applications:

* Middleware
* Logging
* Request timing
* Request IDs
* Security headers
* CORS
* API security fundamentals
* Rate limiting

These are foundational building blocks for building secure, maintainable, and production-ready FastAPI APIs.
