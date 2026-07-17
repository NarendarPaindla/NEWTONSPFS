# Module: Security & Middleware

# Lesson 3 — Request Processing Time Middleware

In production APIs, it's useful to tell clients how long each request took to process. This helps with performance monitoring and debugging.

Instead of only logging the execution time to the console, we'll also return it in the HTTP response.

---

# Learning Objectives

By the end of this lesson, you'll understand how to:

* Measure request execution time
* Add custom HTTP response headers
* Monitor API performance
* Debug slow endpoints

---

# Why Do We Need This?

Suppose your API has these endpoints:

```text
GET    /users
POST   /login
GET    /courses
POST   /payments
```

One day users complain:

> "The courses API is very slow."

If every response contains its execution time, you can quickly identify slow endpoints.

Example response headers:

```text
X-Process-Time: 12.45 ms
```

---

# How It Works

```text
Client
   │
   ▼
Middleware Starts Timer
   │
   ▼
API Endpoint Executes
   │
   ▼
Middleware Calculates Time
   │
   ▼
Adds Header:
X-Process-Time: 5.23 ms
   │
   ▼
Client Receives Response
```

---

# Project Structure

```text
app/
│
├── middleware/
│   ├── __init__.py
│   ├── logging.py
│   └── process_time.py
│
└── main.py
```

---

# File: `app/middleware/process_time.py`

```python
import time

from fastapi import Request


async def process_time_middleware(request: Request, call_next):
    start_time = time.perf_counter()

    response = await call_next(request)

    process_time = (time.perf_counter() - start_time) * 1000

    response.headers["X-Process-Time"] = f"{process_time:.2f} ms"

    return response
```

---

# Update `app/main.py`

```python
from fastapi import FastAPI

from app.middleware.logging import logging_middleware
from app.middleware.process_time import process_time_middleware

app = FastAPI()

app.middleware("http")(logging_middleware)
app.middleware("http")(process_time_middleware)


@app.get("/")
async def home():
    return {
        "message": "SkillHub API Running"
    }


@app.get("/students")
async def students():
    return {
        "students": [
            "Rahul",
            "Anjali",
            "Priya"
        ]
    }
```

---

# Run the Server

```bash
uvicorn app.main:app --reload
```

---

# Test in Swagger

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

---

# Check Response Headers

In Swagger UI or Postman, you'll see:

```text
content-type: application/json

x-process-time: 2.84 ms

content-length: 33
```

The `X-Process-Time` header is added by our middleware.

---

# Middleware Execution Order

Our application now has two middleware:

```python
app.middleware("http")(logging_middleware)
app.middleware("http")(process_time_middleware)
```

Since the **last registered middleware executes first** for incoming requests:

### Incoming Request

```text
Client
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
Client
```

---

# Real-World Uses

This middleware is commonly used for:

* API performance monitoring
* Identifying slow endpoints
* Performance benchmarking
* Observability dashboards
* Application health monitoring

Many monitoring tools (such as Prometheus, Grafana, Datadog, and New Relic) collect similar timing metrics automatically.

---

# Current Middleware Stack

After this lesson, your project contains:

```text
app/
│
├── middleware/
│   ├── logging.py
│   └── process_time.py
│
└── main.py
```

Implemented features:

* ✅ Logging Middleware
* ✅ Request Processing Time Middleware

---
