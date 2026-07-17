# Module: Security & Middleware

# Lesson 6 — CORS (Cross-Origin Resource Sharing)

CORS is one of the most misunderstood topics in backend development.

Many beginners think **FastAPI blocks requests**.

**Wrong.**

The browser blocks the request based on CORS rules.

---

# Learning Objectives

After this lesson, you will understand:

* What CORS is
* What an Origin is
* Same-Origin Policy
* Cross-Origin Requests
* Preflight Requests
* CORSMiddleware
* Production CORS Configuration
* Common Mistakes

---

# What is an Origin?

An **Origin** is made up of three parts:

```text
Protocol + Domain + Port
```

Example:

```text
http://localhost:3000
```

Protocol

```text
http
```

Domain

```text
localhost
```

Port

```text
3000
```

Together these form **one origin**.

---

# Examples

### Same Origin

Frontend

```text
http://localhost:8000
```

Backend

```text
http://localhost:8000
```

Everything matches.

✅ Allowed

---

### Different Port

Frontend

```text
http://localhost:5173
```

Backend

```text
http://localhost:8000
```

Ports differ.

❌ Different Origin

---

### Different Protocol

Frontend

```text
https://localhost:8000
```

Backend

```text
http://localhost:8000
```

Protocols differ.

❌ Different Origin

---

### Different Domain

Frontend

```text
https://myapp.com
```

Backend

```text
https://api.myapp.com
```

Different domains.

❌ Different Origin

---

# Same-Origin Policy (SOP)

Modern browsers enforce the **Same-Origin Policy**.

It means JavaScript can only access resources from the same origin unless the server explicitly allows cross-origin access.

Example:

```text
React App
http://localhost:5173

↓

API Request

↓

http://localhost:8000
```

Browser says

```text
Blocked by CORS Policy
```

The request reaches the server, but the browser blocks JavaScript from accessing the response if the server doesn't send the correct CORS headers.

---

# Real-World Example

Frontend

```text
React

https://app.skillhub.com
```

Backend

```text
FastAPI

https://api.skillhub.com
```

React makes

```http
GET /courses
```

Browser asks

```text
"Is api.skillhub.com allowing app.skillhub.com?"
```

Backend responds with:

```http
Access-Control-Allow-Origin:
https://app.skillhub.com
```

Now the browser allows JavaScript to read the response.

---

# What is a Preflight Request?

Before sending certain requests, the browser sends an **OPTIONS** request.

Example:

```http
OPTIONS /login
```

The server responds with the allowed methods, headers, and origins.

Example response:

```http
Access-Control-Allow-Origin: http://localhost:5173
Access-Control-Allow-Methods: GET, POST
Access-Control-Allow-Headers: Authorization, Content-Type
```

If the browser approves the response, it sends the actual request.

```http
POST /login
```

---

# When Does Preflight Happen?

Usually when:

* Using methods other than `GET`, `HEAD`, or simple `POST`
* Sending custom headers (for example, `Authorization`)
* Sending JSON (`Content-Type: application/json`)
* Using credentials (cookies, authentication)

---

# CORSMiddleware in FastAPI

FastAPI provides a built-in middleware:

```python
from fastapi.middleware.cors import CORSMiddleware
```

---

# Development Configuration

Update your `app/main.py`:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

from app.middleware.logging import logging_middleware
from app.middleware.process_time import process_time_middleware
from app.middleware.request_id import request_id_middleware
from app.middleware.security_headers import security_headers_middleware

app = FastAPI()

# Custom Middleware
app.middleware("http")(logging_middleware)
app.middleware("http")(process_time_middleware)
app.middleware("http")(request_id_middleware)
app.middleware("http")(security_headers_middleware)

# CORS
origins = [
    "http://localhost:5173",
    "http://127.0.0.1:5173",
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)


@app.get("/")
async def home():
    return {
        "message": "SkillHub API Running"
    }
```

---

# Understanding Each Option

## allow_origins

Allowed websites.

```python
allow_origins=[
    "http://localhost:5173",
    "https://app.skillhub.com"
]
```

---

## allow_methods

Allowed HTTP methods.

```python
allow_methods=[
    "GET",
    "POST",
    "PUT",
    "DELETE"
]
```

or

```python
allow_methods=["*"]
```

---

## allow_headers

Allowed request headers.

```python
allow_headers=[
    "Authorization",
    "Content-Type"
]
```

or

```python
allow_headers=["*"]
```

---

## allow_credentials

Allows cookies and authentication credentials.

```python
allow_credentials=True
```

Required if using:

* JWT in cookies
* Session authentication
* Secure cookies

---

# Production Configuration

Instead of:

```python
allow_origins=["*"]
```

Use:

```python
allow_origins=[
    "https://app.skillhub.com"
]
```

Never expose your production API to every website unless it is intentionally a public API.

---

# Common Mistakes

## ❌ Mistake 1

```python
allow_origins=["*"]
allow_credentials=True
```

This is invalid according to the CORS specification and browsers will reject it. If credentials are allowed, you must specify explicit origins.

---

## ❌ Mistake 2

Allowing every website in production:

```python
allow_origins=["*"]
```

This increases your attack surface unnecessarily.

---

## ❌ Mistake 3

Thinking CORS protects the API.

It doesn't.

CORS protects **browsers**, not your backend.

A tool like Postman, curl, or another server can still call your API unless you implement proper authentication and authorization.

---

# Verify in Browser

Suppose your React app runs at:

```text
http://localhost:5173
```

It calls:

```http
GET http://localhost:8000/
```

The response headers should include:

```http
Access-Control-Allow-Origin:
http://localhost:5173

Access-Control-Allow-Credentials:
true
```

Now the browser allows the frontend to access the response.

---

# Current Project Structure

```text
app/
│
├── middleware/
│   ├── logging.py
│   ├── process_time.py
│   ├── request_id.py
│   └── security_headers.py
│
└── main.py
```

Your application now includes:

* ✅ Logging Middleware
* ✅ Request Processing Time Middleware
* ✅ Request ID Middleware
* ✅ Security Headers Middleware
* ✅ CORS Configuration

---

# Interview Questions

### 1. What is CORS?

A browser security mechanism that controls whether a web page from one origin can access resources from another origin.

---

### 2. Does FastAPI block CORS?

No. Browsers enforce CORS. FastAPI only sends the appropriate CORS headers.

---

### 3. What is an Origin?

A combination of **protocol + domain + port**.

---

### 4. What is a Preflight Request?

An `OPTIONS` request sent by the browser to determine whether a cross-origin request is permitted before sending the actual request.

---

### 5. Why shouldn't you use `allow_origins=["*"]` in production?

Because it allows any website to make browser-based cross-origin requests to your API, which is usually broader access than intended.

---

# Next Lesson

We'll cover **Common API Vulnerabilities** based on the OWASP API Security Top 10, including:

* Broken Authentication
* Broken Authorization
* NoSQL Injection
* Sensitive Data Exposure
* Mass Assignment
* Security Misconfiguration
* Brute Force Attacks
* File Upload Vulnerabilities
* Input Validation
* Practical mitigation techniques in FastAPI
