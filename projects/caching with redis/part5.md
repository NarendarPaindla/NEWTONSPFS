# Module: Redis & Caching in FastAPI (Production Level)

# Lesson 5 — FastAPI Integration with `fastapi-cache2` (Production Implementation)

> **Project:** SkillHub API

> **Important:** From this lesson onwards, we start implementing Redis caching in our project.

---

# Learning Objectives

By the end of this lesson, you will learn:

* What is `fastapi-cache2`?
* Why use `fastapi-cache2`?
* Project Architecture
* Installing Dependencies
* Configuring Redis
* Initializing `FastAPICache`
* Redis Backend
* Cache Decorator
* Response Serialization
* Custom Cache Keys
* Complete Project Integration
* Performance Comparison

---

# Before Using fastapi-cache2

Without caching

```text
Client

    │

GET /courses

    │

FastAPI

    │

MongoDB

    │

Return Response
```

Every request goes to MongoDB.

---

After caching

```text
Client

    │

GET /courses

    │

Redis

 ├─────────────► Cache Hit

 │

 └─────────────► Cache Miss

                     │

                  MongoDB

                     │

                 Store Redis

                     │

                 Return Response
```

---

# What is fastapi-cache2?

`fastapi-cache2` is a library that provides **response caching** for FastAPI applications.

Instead of manually writing:

```python
redis.get()

redis.set()

json.dumps()

json.loads()
```

We simply decorate an endpoint.

Example

```python
@cache(expire=300)
```

That's all.

The library handles:

* Serialization
* Deserialization
* Redis Storage
* Cache Lookup
* TTL
* Response Restoration

Automatically.

---

# Why Use fastapi-cache2?

Without library

```python
data = await redis.get(key)

if data:
    return json.loads(data)

result = await database.fetch()

await redis.set(...)

return result
```

Every endpoint repeats this logic.

---

With fastapi-cache2

```python
@cache(expire=300)
```

Cleaner.

Smaller.

Less bugs.

---

# Installation

Install Redis client

```bash
pip install redis
```

Install cache library

```bash
pip install fastapi-cache2
```

---

# Project Structure

After today's lesson

```text
app/

├── api/
│      courses.py
│
├── core/
│      config.py
│      redis.py
│
├── services/
│
├── main.py
│
└── ...
```

---

# How fastapi-cache2 Works

```text
Client

    │

GET /courses

    │

FastAPI

    │

@cache

    │

Redis

 │

 ├── Hit

 │      │

 │      ▼

 │  Return Response

 │

 └── Miss

         │

     Execute API

         │

     Store Result

         │

      Return Client
```

---

# Components

There are four important components.

---

## 1. Redis Connection

Connects

↓

Redis Server

---

## 2. Redis Backend

Tells

FastAPICache

Where to store data.

Example

```text
Redis

Memory

↓

Backend
```

---

## 3. FastAPICache

Global cache manager.

Responsible for

* initialization
* serialization
* cache lookup

---

## 4. Cache Decorator

Example

```python
@cache(expire=300)
```

Every request goes through the decorator.

---

# Request Lifecycle

```text
GET /courses

      │

      ▼

Decorator

      │

Redis

      │

Hit?

 ├── Yes

 │      ▼

 │  Return Cached Response

 │

 └── No

        ▼

 Execute Endpoint

        ▼

 Save Redis

        ▼

 Return Client
```

---

# Serialization

Redis cannot directly store

Python Objects.

Suppose

```python
{
   "id":1,
   "name":"Python"
}
```

Redis stores

```text
Bytes
```

The cache library automatically

* Serializes

↓

Stores

↓

Deserializes

↓

Returns Python response

No manual conversion required.

---

# Cache Expiration

Example

```python
@cache(expire=600)
```

Meaning

```text
TTL

600 Seconds

↓

10 Minutes
```

---

# Namespace

Suppose

Courses

Students

Dashboard

All cached.

Namespaces separate them.

Example

```text
course

student

dashboard
```

Better organization.

---

# Custom Cache Keys

By default

Library creates cache keys automatically.

But in production

We often use

our own

Cache Key Builder.

Example

```text
course:list

course:15

student:5
```

We'll integrate our `CacheKeys` helper with the library in a later lesson.

---

# Performance Comparison

Without Cache

```text
Database

↓

180 ms
```

---

With Cache

```text
Redis

↓

2 ms
```

The actual improvement depends on:

* Network latency
* Database performance
* Response size
* Serialization overhead

But caching frequently reduces response times significantly.

---

# Real Industry Example

Suppose

100,000 users

Open homepage.

Homepage needs

```http
GET /courses
```

Without cache

```text
100,000 Database Queries
```

---

With cache

```text
1 Database Query

↓

99,999 Redis Reads
```

Huge reduction in database load.

---

# When NOT to Use the Decorator

Avoid decorating:

```http
POST /login

POST /logout

POST /payment

POST /verify-otp
```

Reason

They are

* write operations
* security-sensitive
* user-specific

Caching them can cause incorrect behavior.

---

# Typical Cache Flow in SkillHub

```text
Student

      │

GET /courses

      │

@cache

      │

Redis

 │

 ├── Hit

 │      ▼

 │  Return

 │

 └── Miss

        ▼

 MongoDB

        ▼

 Redis

        ▼

 Student
```

---

# Production Best Practices

✅ Cache only GET APIs.

✅ Use meaningful TTL values.

✅ Combine caching with invalidation.

✅ Do not cache authentication responses.

✅ Keep cache initialization centralized.

✅ Use Redis as the backend instead of in-memory cache for distributed deployments.

---

# Interview Questions

### 1. What is `fastapi-cache2`?

A library that provides automatic response caching for FastAPI applications using backends such as Redis.

---

### 2. Why use `fastapi-cache2` instead of manually calling Redis?

It reduces boilerplate code, automatically handles serialization/deserialization, manages TTLs, and makes endpoint caching simple through decorators.

---

### 3. What does the `@cache` decorator do?

It checks whether a cached response exists. If it does, the cached response is returned. Otherwise, the endpoint executes, its response is cached, and then returned.

---

### 4. Should all endpoints use `@cache`?

No. It is generally appropriate for read-heavy `GET` endpoints, not for write operations or security-sensitive APIs.

---

### 5. Does `fastapi-cache2` replace Redis?

No. It is a library that uses Redis (or another supported backend) to store cached data. Redis is still the cache server.

---

# ⚠️ Important Note

Today we covered the **architecture and concepts** of integrating `fastapi-cache2`.

**Starting in the next lesson**, we'll begin writing the complete project code, including:

* `app/core/redis.py`
* Redis initialization
* `FastAPICache.init()`
* Redis backend configuration
* Startup integration
* Caching the first SkillHub API endpoint with complete updated files

This approach keeps the concepts clear before introducing production code.

---

# Module Progress

* ✅ Lesson 1 — Redis Basics
* ✅ Lesson 2 — When to Cache vs When NOT to Cache
* ✅ Lesson 3 — Cache Key Design
* ✅ Lesson 4 — TTL
* ✅ Lesson 5 — FastAPI Integration with `fastapi-cache2` (Concepts)

---

# Next Lesson

**Lesson 6 — Complete Project Implementation with `fastapi-cache2`**

In this lesson, we'll write the **actual production code** for the SkillHub project, including:

* Redis configuration
* `redis.py`
* `config.py` updates
* `main.py` updates
* Cache initialization
* First cached API endpoint
* Complete updated files (no code snippets) following the project architecture.
