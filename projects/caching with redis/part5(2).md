I want to point out one thing before we continue.

In the original syllabus, **Lesson 5** was **"FastAPI Integration with `fastapi-cache2`"**. That lesson should include the **actual implementation**, not just the concepts. In the previous response, we mainly covered the theory.

So, from this lesson onward, we'll begin the **real coding** for the SkillHub project using complete files (as you've requested throughout this course). We won't postpone the implementation any further.

---

# Module: Redis & Caching in FastAPI (Production Level)

# Lesson 5 (Part 2) — Complete FastAPI Integration with `fastapi-cache2`

> **Project:** SkillHub API

---

# Learning Objectives

In this lesson, we will:

* Install and configure Redis
* Connect FastAPI with Redis
* Initialize `FastAPICache`
* Create a reusable Redis client
* Configure Redis through environment variables
* Add Redis startup and shutdown lifecycle
* Prepare the project for endpoint caching in the next lesson

---

# Step 1 — Install Dependencies

```bash
pip install redis fastapi-cache2
```

---

# Updated Project Structure

```text
app/
│
├── api/
│
├── core/
│   ├── config.py
│   ├── redis.py
│   └── cache_keys.py
│
├── services/
├── models/
├── schemas/
├── main.py
│
.env
```

---

# File 1 — `app/core/config.py`

> **Complete File**

```python
from functools import lru_cache

from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    APP_NAME: str = "SkillHub API"

    REDIS_HOST: str = "localhost"
    REDIS_PORT: int = 6379
    REDIS_DB: int = 0
    REDIS_PASSWORD: str | None = None

    model_config = SettingsConfigDict(
        env_file=".env",
        extra="ignore"
    )


@lru_cache
def get_settings() -> Settings:
    return Settings()


settings = get_settings()
```

---

# File 2 — `app/core/redis.py`

> **Complete File**

```python
from redis.asyncio import Redis

from app.core.config import settings

redis_client = Redis(
    host=settings.REDIS_HOST,
    port=settings.REDIS_PORT,
    db=settings.REDIS_DB,
    password=settings.REDIS_PASSWORD,
    decode_responses=True
)
```

---

# Why `decode_responses=True`?

Without it

```python
b'Python'
```

With it

```python
"Python"
```

It automatically converts bytes into normal Python strings.

---

# File 3 — `.env`

```text
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DB=0
REDIS_PASSWORD=
```

---

# File 4 — `app/main.py`

> **Complete File**

```python
from contextlib import asynccontextmanager

from fastapi import FastAPI
from fastapi_cache import FastAPICache
from fastapi_cache.backends.redis import RedisBackend

from app.core.config import settings
from app.core.redis import redis_client


@asynccontextmanager
async def lifespan(app: FastAPI):
    await redis_client.ping()

    FastAPICache.init(
        RedisBackend(redis_client),
        prefix="skillhub-cache"
    )

    print("Redis Connected")
    print("FastAPI Cache Initialized")

    yield

    await redis_client.close()


app = FastAPI(
    title=settings.APP_NAME,
    lifespan=lifespan
)
```

---

# What Happens During Startup?

```text
FastAPI Starts

        │

        ▼

Connect Redis

        │

        ▼

PING Redis

        │

        ▼

Initialize FastAPICache

        │

        ▼

Application Ready
```

---

# Why Use Lifespan Instead of `@app.on_event`?

Older versions of FastAPI commonly used:

```python
@app.on_event("startup")
```

and

```python
@app.on_event("shutdown")
```

The recommended modern approach is the **lifespan** context manager because it manages startup and shutdown logic in one place and aligns with current FastAPI practices.

---

# What is `RedisBackend`?

`FastAPICache` supports different storage backends.

Examples:

```text
Redis

Memcached

In-Memory
```

We choose

```python
RedisBackend(redis_client)
```

because Redis is suitable for production deployments.

---

# What Does `prefix="skillhub-cache"` Do?

Suppose Redis stores

```text
course:list
```

Internally, the library stores it like:

```text
skillhub-cache::course:list
```

Benefits:

* Avoids collisions with other applications using the same Redis instance.
* Makes cache management easier.
* Enables safer cleanup.

---

# Verify Redis Connection

Open another terminal.

```bash
redis-cli
```

Run

```bash
PING
```

Output

```text
PONG
```

Now start FastAPI.

Expected console output:

```text
Redis Connected

FastAPI Cache Initialized
```

---

# Redis Storage

After initialization

Redis memory looks like

```text
Redis

│

├── skillhub-cache::

├── course:list

├── course:10

├── dashboard

└── ...
```

Actual keys will appear only after cached endpoints are accessed.

---

# Current Architecture

```text
Client

     │

FastAPI

     │

FastAPICache

     │

Redis Backend

     │

Redis Server
```

No endpoint is cached yet.

We have only connected everything.

---

# Common Mistakes

## Forgetting to Initialize `FastAPICache`

```python
@cache(expire=300)
```

Without

```python
FastAPICache.init(...)
```

Result:

```text
Cache not initialized
```

---

## Redis Not Running

If Redis is stopped,

```text
Connection Refused
```

Always verify the server is running before starting FastAPI.

---

## Wrong Redis Port

Default Redis port:

```text
6379
```

If your `.env` specifies a different port, ensure it matches your Redis server configuration.

---

# Production Best Practices

* ✅ Store Redis settings in environment variables.
* ✅ Initialize `FastAPICache` once during application startup.
* ✅ Use a unique cache prefix for your application.
* ✅ Verify the Redis connection during startup so issues are detected early.
* ✅ Close the Redis client during application shutdown.

---

# Interview Questions

### 1. Why do we initialize `FastAPICache` during application startup?

To configure the cache backend once before any request is processed.

---

### 2. Why use `RedisBackend`?

It enables `FastAPICache` to store cached responses in Redis, which supports fast, centralized caching suitable for production.

---

### 3. What is the purpose of the cache prefix?

It namespaces cache entries, preventing collisions with keys from other applications sharing the same Redis instance.

---

### 4. Why use environment variables for Redis configuration?

They separate configuration from code and allow different settings for development, staging, and production.

---

### 5. What happens if Redis is unavailable during startup?

The application may fail to initialize the cache correctly. Production applications should log the error and choose an appropriate strategy (fail fast or continue without caching) based on business requirements.

---

# Module Progress

* ✅ Lesson 1 — Redis Basics
* ✅ Lesson 2 — When to Cache vs When NOT to Cache
* ✅ Lesson 3 — Cache Key Design
* ✅ Lesson 4 — TTL
* ✅ Lesson 5 — FastAPI Integration with `fastapi-cache2` (Complete Integration)

---

# Next Lesson

**Lesson 6 — Caching Real SkillHub APIs**

In the next lesson, we'll implement caching for actual endpoints such as:

* `GET /courses`
* `GET /courses/{id}`
* `GET /skills`

We'll use the `@cache` decorator, custom cache keys, TTLs, and measure the performance improvement with complete updated project files.
