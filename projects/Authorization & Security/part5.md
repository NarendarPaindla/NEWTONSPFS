# Module: Security & Middleware

# Lesson 5 — Security Headers Middleware

In this lesson, we'll add **HTTP Security Headers** to every response. These headers help protect your FastAPI application against common web attacks such as **Clickjacking, MIME Sniffing, Cross-Site Scripting (XSS), and Information Leakage**.

This is a standard practice in production applications.

---

# Learning Objectives

By the end of this lesson, you will understand:

* What security headers are
* Why they are important
* How to add them using middleware
* What each header does
* Best practices for production

---

# What are Security Headers?

Security headers are HTTP response headers that instruct the browser how to handle your application securely.

Example Response:

```http
HTTP/1.1 200 OK

Content-Type: application/json
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Content-Security-Policy: default-src 'self'
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

These headers are invisible to users but are enforced by modern browsers.

---

# Why Do We Need Security Headers?

Suppose your API is accessed through a web application.

Without security headers:

* Attackers can embed your website inside an iframe.
* Browsers may guess incorrect content types.
* Malicious JavaScript may execute.
* Sensitive URLs may leak to third-party websites.
* HTTPS protection may be bypassed.

Security headers reduce these risks.

---

# Common Security Headers

## 1. X-Content-Type-Options

```http
X-Content-Type-Options: nosniff
```

### Purpose

Prevents browsers from guessing the file type.

Without it:

```text
Server sends:
image.png

Browser guesses:
JavaScript
```

This could allow malicious code execution.

With:

```http
nosniff
```

The browser only trusts the declared `Content-Type`.

---

## 2. X-Frame-Options

```http
X-Frame-Options: DENY
```

### Purpose

Protects against **Clickjacking**.

Without it:

An attacker could load your site inside an invisible iframe.

```text
Attacker Website
──────────────────────────────

[ Invisible Bank Website ]

[ Click Here to Win ₹1000 ]
```

The user thinks they are clicking a button on the attacker's page but is actually clicking your application.

Using:

```http
DENY
```

prevents your application from being embedded in any iframe.

Other values:

```text
DENY
SAMEORIGIN
```

---

## 3. Referrer-Policy

```http
Referrer-Policy: strict-origin-when-cross-origin
```

### Purpose

Controls how much URL information is shared when users navigate to another website.

Example:

Current page:

```text
https://skillhub.com/profile?id=25
```

Without protection:

```text
https://another-site.com

Receives:

https://skillhub.com/profile?id=25
```

Sensitive query parameters could leak.

Using:

```http
strict-origin-when-cross-origin
```

Only the origin (`https://skillhub.com`) is shared.

---

## 4. Content-Security-Policy (CSP)

```http
Content-Security-Policy: default-src 'self'
```

### Purpose

Restricts where scripts, images, CSS, fonts, and other resources can be loaded from.

Example:

Allowed:

```text
https://skillhub.com/script.js
```

Blocked:

```text
https://evil.com/malware.js
```

A properly configured CSP is one of the strongest defenses against XSS attacks.

---

## 5. Strict-Transport-Security (HSTS)

```http
Strict-Transport-Security:
max-age=31536000; includeSubDomains
```

### Purpose

Forces browsers to use **HTTPS**.

Without HSTS:

```text
User

↓

http://skillhub.com

↓

Attacker intercepts traffic
```

With HSTS:

```text
Browser

↓

Automatically converts

↓

https://skillhub.com
```

This helps prevent SSL stripping attacks.

> **Note:** Enable HSTS only when your application is served over HTTPS in production.

---

# Project Structure

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

---

# Step 1 — Create Security Headers Middleware

## File: `app/middleware/security_headers.py`

```python
from fastapi import Request


async def security_headers_middleware(request: Request, call_next):
    response = await call_next(request)

    response.headers["X-Content-Type-Options"] = "nosniff"
    response.headers["X-Frame-Options"] = "DENY"
    response.headers["Referrer-Policy"] = "strict-origin-when-cross-origin"
    response.headers["Content-Security-Policy"] = "default-src 'self'"

    # Enable this only when your application uses HTTPS
    response.headers["Strict-Transport-Security"] = (
        "max-age=31536000; includeSubDomains"
    )

    return response
```

---

# Step 2 — Register the Middleware

## Update `app/main.py`

```python
from fastapi import FastAPI

from app.middleware.request_id import request_id_middleware
from app.middleware.logging import logging_middleware
from app.middleware.process_time import process_time_middleware
from app.middleware.security_headers import security_headers_middleware

app = FastAPI()

app.middleware("http")(logging_middleware)
app.middleware("http")(process_time_middleware)
app.middleware("http")(request_id_middleware)
app.middleware("http")(security_headers_middleware)


@app.get("/")
async def home():
    return {
        "message": "SkillHub API Running"
    }
```

---

# Test the API

Start the server:

```bash
uvicorn app.main:app --reload
```

Open Swagger:

```text
http://127.0.0.1:8000/docs
```

Call:

```http
GET /
```

Response:

```json
{
    "message": "SkillHub API Running"
}
```

---

# Check Response Headers

You should now see headers similar to:

```http
Content-Type: application/json

X-Request-ID: 3c8a5a2c-9a5c-4d11-a0d3-3c5bcd2d9e18

X-Process-Time: 2.31 ms

X-Content-Type-Options: nosniff

X-Frame-Options: DENY

Referrer-Policy: strict-origin-when-cross-origin

Content-Security-Policy: default-src 'self'

Strict-Transport-Security: max-age=31536000; includeSubDomains
```

---

# Middleware Stack So Far

```text
Client
   │
   ▼
Security Headers Middleware
   ▼
Request ID Middleware
   ▼
Process Time Middleware
   ▼
Logging Middleware
   ▼
API Route
```

On the response, the execution order is reversed.

---

# Best Practices

* ✅ Always use `X-Content-Type-Options: nosniff`.
* ✅ Use `X-Frame-Options: DENY` unless your app must be embedded in an iframe.
* ✅ Configure `Content-Security-Policy` carefully based on your frontend's requirements.
* ✅ Use `Referrer-Policy` to minimize information leakage.
* ✅ Enable `Strict-Transport-Security` **only in production over HTTPS**. Do **not** enable HSTS during local HTTP development.

---

# Project Status

Implemented so far:

* ✅ Logging Middleware
* ✅ Request Processing Time Middleware
* ✅ Request ID Middleware
* ✅ Security Headers Middleware

---

# Next Lesson

We'll implement **CORS (Cross-Origin Resource Sharing)** using FastAPI's built-in `CORSMiddleware`. You'll learn:

* Same-Origin Policy
* What causes CORS errors
* How browsers enforce CORS
* Configuring `allow_origins`, `allow_methods`, `allow_headers`, and `allow_credentials`
* Secure CORS settings for development and production
