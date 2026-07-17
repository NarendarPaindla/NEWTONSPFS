# Lesson 2 — Custom Logging Middleware

In this lesson, we'll build a **Logging Middleware** for the SkillHub API. It will log details of every incoming request and outgoing response, which is essential for debugging, monitoring, and auditing in production.

---

# What is Logging Middleware?

Logging middleware records information about each HTTP request and response before and after the request is processed.

Typical information logged includes:

* HTTP Method (GET, POST, PUT, DELETE)
* Request URL
* Client IP Address
* Response Status Code
* Time Taken to Process the Request

---

# Why is it Useful?

Imagine a user reports:

> "The login API is not working."

Without logs:

* You don't know when the request was made.
* You don't know which endpoint was called.
* You don't know whether the request reached the server.

With logs, you can immediately see something like:

```text
2026-07-17 10:15:30 | POST | /api/v1/auth/login | 200 | 25.41 ms
```

This helps identify issues much faster.

---

# Project Structure

We'll create the middleware inside the `middleware` package.

```text
app/
│
├── middleware/
│   ├── __init__.py
│   └── logging.py
│
├── main.py
```

---

# File: `app/middleware/logging.py`

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

    logger.info(
        f"Incoming Request -> {request.method} {request.url.path}"
    )

    response = await call_next(request)

    process_time = (time.perf_counter() - start_time) * 1000

    logger.info(
        f"Outgoing Response -> {response.status_code} | {process_time:.2f} ms"
    )

    return response
```

---

# Register the Middleware

### File: `app/main.py`

```python
from fastapi import FastAPI

from app.middleware.logging import logging_middleware

app = FastAPI()


app.middleware("http")(logging_middleware)


@app.get("/")
async def home():
    return {
        "message": "SkillHub API Running"
    }
```

---

# Run the Server

```bash
uvicorn app.main:app --reload
```

---

# Test the API

Open your browser or Swagger UI and access:

```text
GET /
```

Response:

```json
{
    "message": "SkillHub API Running"
}
```

---

# Console Output

```text
2026-07-17 11:20:01 | INFO | Incoming Request -> GET /
2026-07-17 11:20:01 | INFO | Outgoing Response -> 200 | 3.15 ms
```

---

# How It Works

1. A client sends a request.
2. The middleware records the start time.
3. It logs the request method and URL.
4. `call_next(request)` forwards the request to the API endpoint.
5. After the endpoint returns a response, the middleware calculates the processing time.
6. It logs the response status code and execution time.
7. The response is returned to the client.

---

# Production Improvements

In real-world applications, logging middleware is often extended to include:

* Request ID
* Client IP Address
* User ID (after authentication)
* Query Parameters
* Response Size
* Error Details
* Log Rotation
* Structured JSON Logs
* Integration with monitoring tools like ELK, Grafana, or Datadog

Our next lesson will improve this middleware by adding **Request Processing Time Middleware**, so every API response includes its execution time in an HTTP response header.
