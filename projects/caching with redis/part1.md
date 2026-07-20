# Module: Redis & Caching in FastAPI (Production Level)

# Lesson 1 — Redis Basics (Production Ready)

> **Project:** SkillHub API

---

# Learning Objectives

By the end of this lesson, you will understand:

* What is Redis?
* Why Redis was created
* How Redis works internally
* Redis Architecture
* In-Memory Database
* Redis Server
* Redis Client
* Redis CLI
* Redis Data Types (Overview)
* Redis Persistence
* Advantages & Limitations
* Real-world Use Cases
* Connect FastAPI to Redis (Project Implementation)

---

# Before Redis

Imagine our SkillHub API.

When a user requests:

```http
GET /courses
```

The request flow is:

```text
Client
   │
   ▼
FastAPI
   │
   ▼
MongoDB
   │
   ▼
Read Data
   │
   ▼
FastAPI
   │
   ▼
Client
```

Now imagine:

* 10,000 students
* 2,000 requests every minute

Every request goes to MongoDB.

Problems:

* Database CPU increases
* Disk I/O increases
* Queries become slower
* Response time increases
* Database becomes the bottleneck

---

# What is Redis?

**Redis (Remote Dictionary Server)** is an **in-memory data store** that stores data primarily in RAM instead of on disk.

Unlike traditional databases:

| Database   | Storage                    |
| ---------- | -------------------------- |
| MySQL      | Disk                       |
| PostgreSQL | Disk                       |
| MongoDB    | Disk (with memory caching) |
| Redis      | RAM (Memory)               |

Since RAM is much faster than disk, Redis can serve data extremely quickly.

---

# Why Redis?

Suppose fetching a course list from MongoDB takes:

```text
Database Query = 180 ms
```

After storing the result in Redis:

```text
Redis Lookup = 2 ms
```

The database isn't queried for every request.

---

# Real-World Example

Consider a SkillHub homepage.

Every student opens:

```http
GET /courses
```

The list changes only a few times each day.

Without Redis:

```text
10,000 Requests

↓

10,000 MongoDB Queries
```

With Redis:

```text
First Request

↓

MongoDB

↓

Redis

↓

Remaining Requests

↓

Redis Only
```

The database handles one query instead of thousands until the cache expires or is invalidated.

---

# Why is Redis So Fast?

Traditional databases:

```text
Application

↓

Disk

↓

Read Data

↓

Return
```

Disk access is relatively slow.

Redis:

```text
Application

↓

RAM

↓

Return Data
```

Memory access is significantly faster.

---

# Redis Architecture

```text
                FastAPI

                   │

         Redis Python Client

                   │

             TCP Connection

                   │

             Redis Server

                   │

        In-Memory Key-Value Store

                   │

        Optional Persistence (Disk)
```

Redis is a separate server that your application communicates with over the network.

---

# Redis Components

## 1. Redis Server

The process that stores data and handles requests.

Example:

```text
redis-server
```

---

## 2. Redis Client

Your FastAPI application connects to Redis using a client library such as `redis-py`.

```python
redis_client.get(...)
```

The client sends commands to the Redis server.

---

## 3. Redis CLI

A command-line tool used to interact with Redis manually.

```bash
redis-cli
```

Example:

```bash
SET name Narendar
```

```bash
GET name
```

Output:

```text
"Narendar"
```

---

# How Redis Works Internally

Redis stores data as **key-value pairs**.

Example:

```text
Key

course:list
```

↓

Value

```json
[
  {
    "id": 1,
    "name": "Python"
  },
  {
    "id": 2,
    "name": "FastAPI"
  }
]
```

Redis retrieves data using the key.

---

# Redis Request Lifecycle

```text
FastAPI

      │

GET course:list

      │

Redis

      │

Find Key

      │

Return Value

      │

FastAPI

      │

Client
```

If the key doesn't exist, Redis returns nothing, and your application can fetch the data from MongoDB and populate the cache.

---

# Redis Data Types (Overview)

Redis supports multiple data structures.

| Data Type   | Typical Use Case             |
| ----------- | ---------------------------- |
| String      | Cached API responses, tokens |
| Hash        | User profiles                |
| List        | Recent activities            |
| Set         | Unique tags, skills          |
| Sorted Set  | Leaderboards                 |
| Stream      | Event processing             |
| Bitmap      | Feature flags, analytics     |
| HyperLogLog | Approximate unique counts    |

We'll use the appropriate data type in later lessons.

---

# Redis Persistence

Although Redis is memory-based, it can optionally persist data to disk.

Two common persistence mechanisms are:

### RDB (Redis Database Snapshot)

* Periodically saves snapshots of memory to disk.
* Good for backups.
* Faster recovery with point-in-time snapshots.

---

### AOF (Append Only File)

* Logs every write operation.
* Better durability.
* Can reconstruct the dataset by replaying commands.

Many production systems use both, depending on durability requirements.

---

# Advantages of Redis

* Extremely fast reads and writes
* Simple key-value model
* Supports multiple data structures
* TTL support
* High throughput
* Suitable for caching, sessions, queues, counters, and more

---

# Limitations of Redis

* RAM is more expensive than disk.
* Large datasets can consume significant memory.
* Not every type of data should be cached.
* Poor cache design can lead to stale or inconsistent data.

---

# Real-World Redis Use Cases

| Feature              | Redis Usage                     |
| -------------------- | ------------------------------- |
| API Response Caching | Cache frequently requested data |
| Sessions             | Store logged-in user sessions   |
| OTP Storage          | Temporary verification codes    |
| Rate Limiting        | Track request counts            |
| Leaderboards         | Sorted Sets                     |
| Notifications        | Pub/Sub                         |
| Background Jobs      | Queues                          |
| Shopping Carts       | Temporary cart state            |

---

# Project Implementation — SkillHub API

In this module, we'll integrate Redis into the existing SkillHub API.

By the end of the module, we'll cache endpoints such as:

```http
GET /courses
GET /courses/{id}
GET /skills
GET /dashboard
```

We'll also implement:

* Cache keys
* TTL
* Automatic invalidation
* Stampede protection

---

# Step 1 — Install Redis Server

## Windows

1. Install Docker Desktop.
2. Pull the Redis image:

```bash
docker pull redis:7
```

3. Start a Redis container:

```bash
docker run -d ^
  --name skillhub-redis ^
  -p 6379:6379 ^
  redis:7
```

Verify it's running:

```bash
docker ps
```

---

## Linux

```bash
sudo apt update
sudo apt install redis-server
```

Start Redis:

```bash
sudo systemctl start redis
```

Enable it on boot:

```bash
sudo systemctl enable redis
```

---

## macOS

```bash
brew install redis
brew services start redis
```

---

# Step 2 — Verify Redis

Open a terminal:

```bash
redis-cli
```

Test the server:

```bash
PING
```

Expected output:

```text
PONG
```

---

# Step 3 — Install Python Client

We'll use the official asynchronous Redis client.

```bash
pip install redis
```

---

# Project Structure (New Files)

After today's lesson, our project will evolve like this:

```text
app/
├── core/
│   ├── config.py
│   └── redis.py      ← New
├── api/
├── services/
├── models/
├── schemas/
└── main.py
```

---

# What We'll Build Next

In the next lesson (**When to Cache vs When Not to Cache**), we'll start writing code by:

* Creating a reusable Redis connection manager
* Initializing Redis during FastAPI startup
* Connecting `fastapi-cache2` to Redis
* Caching the `GET /courses` endpoint
* Measuring the performance improvement

At that point, we'll begin updating project files with complete implementations, following the "complete file, no snippets" approach you've requested throughout this course.
