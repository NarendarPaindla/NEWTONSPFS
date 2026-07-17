# Module: Security & Middleware

# Lesson 4 — Request ID Middleware

This is one of the most commonly used middleware in production applications.

When thousands of users access your API simultaneously, it becomes difficult to determine which log messages belong to which request.

A **Request ID** solves this problem by assigning a unique identifier to every incoming request.

---

# Learning Objectives

After this lesson, you'll understand:

* What a Request ID is
* Why it is important
* How to generate a unique Request ID
* How to send it back to the client
* How to use it in logs

---

# The Problem

Imagine 500 users access the login API at the same time.

Without Request IDs, your logs look like this:

```text
Incoming Request -> POST /login
Incoming Request -> POST /login
Incoming Request -> POST /login

Outgoing Response -> 200
Outgoing Response -> 401
Outgoing Response -> 500
```

Which response belongs to which request?

You can't tell.

---

# The Solution

Generate a unique ID for every request.

Example:

```text
Request A → 7f3d6b45-2d21-4fd0-b965-43e5ec4f7b8f

Request B → 52acb0ef-99bc-42df-ae3b-742d9dc60c19

Request C → 4ce73bdb-917d-4e9e-bdf7-ae1ef14f95ba
```

Now every log entry is linked to one request.

---

# Request Flow

```text
Client
   │
   ▼
Generate UUID
   │
   ▼
Store in request.state
   │
   ▼
API Endpoint
   │
   ▼
Response
   │
   ▼
Add Header
X-Request-ID
   │
   ▼
Client
```

---

# Project Structure

```text
app/
│
├── middleware/
│   ├── logging.py
│   ├── process_time.py
│   └── request_id.py
│
└── main.py
```

---

# Step 1 — Create Request ID Middleware

## File: `app/middleware/request_id.py`

```python
import uuid

from fastapi import Request


async def request_id_middleware(request: Request, call_next):
    request_id = str(uuid.uuid4())

    request.state.request_id = request_id

    response = await call_next(request)

    response.headers["X-Request-ID"] = request_id

    return response
```

---

# What is `request.state`?

FastAPI provides `request.state` to store custom data during the lifetime of a request.

```python
request.state.request_id = request_id
```

This value is available anywhere that has access to the same `Request` object.

Example:

```python
@app.get("/profile")
async def profile(request: Request):
    return {
        "request_id": request.state.request_id
    }
```

---

# Step 2 — Update Logging Middleware

Modify `app/middleware/logging.py`

```python
import time
import logging

from fastapi import Request

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(message)s"
)

logger = logging.getLogger("skillhub")


async def logging_middleware(request: Request, call_next):
    start_time = time.perf_counter()

    request_id = getattr(request.state, "request_id", "N/A")

    logger.info(
        f"[{request_id}] Incoming {request.method} {request.url.path}"
    )

    response = await call_next(request)

    process_time = (time.perf_counter() - start_time) * 1000

    logger.info(
        f"[{request_id}] Response {response.status_code} | {process_time:.2f} ms"
    )

    return response
```

---

# Step 3 — Update `main.py`

```python
from fastapi import FastAPI

from app.middleware.request_id import request_id_middleware
from app.middleware.logging import logging_middleware
from app.middleware.process_time import process_time_middleware

app = FastAPI()

app.middleware("http")(logging_middleware)
app.middleware("http")(process_time_middleware)
app.middleware("http")(request_id_middleware)


@app.get("/")
async def home():
    return {
        "message": "SkillHub API Running"
    }
```

---

# Middleware Execution Order

Because the last middleware added runs first on incoming requests:

```python
app.middleware("http")(logging_middleware)
app.middleware("http")(process_time_middleware)
app.middleware("http")(request_id_middleware)
```

### Incoming Request

```text
Client
   │
   ▼
Request ID Middleware
   │
   ▼
Process Time Middleware
   │
   ▼
Logging Middleware
   │
   ▼
Route
```

### Outgoing Response

```text
Route
   │
   ▼
Logging Middleware
   │
   ▼
Process Time Middleware
   │
   ▼
Request ID Middleware
   │
   ▼
Client
```

Since the Request ID is created first, it is available to all subsequent middleware.

---

# Test the API

Request:

```http
GET /
```

Response:

```json
{
    "message": "SkillHub API Running"
}
```

Response Headers:

```text
X-Request-ID: 7f3d6b45-2d21-4fd0-b965-43e5ec4f7b8f

X-Process-Time: 2.18 ms
```

Console Logs:

```text
2026-07-17 10:15:32 | INFO | [7f3d6b45-2d21-4fd0-b965-43e5ec4f7b8f] Incoming GET /

2026-07-17 10:15:32 | INFO | [7f3d6b45-2d21-4fd0-b965-43e5ec4f7b8f] Response 200 | 2.18 ms
```

Now you can correlate every log entry with a specific request.

---

# Real-World Uses

Request IDs are widely used for:

* Distributed microservices
* API Gateway tracing
* Debugging production issues
* Centralized logging systems
* Error tracking
* Performance monitoring
* Customer support investigations

Many cloud platforms (AWS, Azure, GCP) and observability tools (OpenTelemetry, Jaeger, Zipkin) rely on request or trace IDs to follow requests across multiple services.

---

# Project Status

Current middleware implemented:

* ✅ Logging Middleware
* ✅ Request Processing Time Middleware
* ✅ Request ID Middleware

---

# Next Lesson

We'll implement **Security Headers Middleware**, where we'll add important HTTP security headers such as:

* `X-Content-Type-Options`
* `X-Frame-Options`
* `Strict-Transport-Security`
* `Referrer-Policy`
* `Content-Security-Policy`

These headers help protect your FastAPI application from common web attacks such as clickjacking, MIME sniffing, and content injection.
