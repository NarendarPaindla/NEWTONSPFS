# Module: Redis & Caching in FastAPI (Production Level)

# Lesson 2 — When to Cache vs When NOT to Cache (Production Strategy)

> **Project:** SkillHub API

---

# Learning Objectives

By the end of this lesson, you will understand:

* What is Caching?
* Why Caching is Needed
* Cache Hit
* Cache Miss
* Cache-Aside Pattern (Lazy Loading)
* Read-Through Cache
* Write-Through Cache
* Write-Behind Cache
* Refresh-Ahead Cache
* When to Cache
* When NOT to Cache
* Real-world Decision Making
* SkillHub API Caching Strategy

---

# What is Caching?

Caching is the process of **temporarily storing frequently used data in a faster storage system** (such as Redis) so future requests can be served without repeatedly querying the primary database.

Without cache:

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
FastAPI
   │
   ▼
Client
```

Every request hits MongoDB.

---

With cache:

```text
Client
   │
   ▼
FastAPI
   │
   ▼
Redis
   │
   ├──────────► Cache Hit
   │               │
   │               ▼
   │          Return Data
   │
   ▼
Cache Miss
   │
MongoDB
   │
Store in Redis
   │
Return Response
```

---

# Why Do We Need Cache?

Suppose:

```http
GET /courses
```

Execution time:

| Operation          | Time   |
| ------------------ | ------ |
| MongoDB Query      | 180 ms |
| JSON Serialization | 20 ms  |
| Total              | 200 ms |

Now imagine:

50,000 students

Every student opens the homepage.

Without cache:

```text
50,000 Requests

↓

50,000 Database Queries
```

---

With Redis:

```text
First Request

↓

MongoDB

↓

Redis

↓

49,999 Requests

↓

Redis
```

Database load becomes dramatically lower.

---

# Cache Hit

Definition:

The requested data already exists inside Redis.

Example

```http
GET /courses
```

Redis contains

```text
course:list
```

Flow

```text
Client

↓

FastAPI

↓

Redis

↓

Data Found

↓

Return Response
```

Database is **not accessed**.

---

# Cache Miss

Definition:

Requested data is not available in Redis.

Flow

```text
Client

↓

FastAPI

↓

Redis

↓

Not Found

↓

MongoDB

↓

Redis

↓

Client
```

After MongoDB returns data,

FastAPI stores it inside Redis.

---

# Cache Lifecycle

```text
First Request

↓

Redis Miss

↓

MongoDB

↓

Store in Redis

↓

Return Data

↓

Next Requests

↓

Redis Hit
```

This is the most common caching lifecycle.

---

# Cache-Aside Pattern (Lazy Loading)

This is the **most widely used caching strategy** and the one we'll implement in the SkillHub API.

### Workflow

```text
Client

↓

Application

↓

Check Redis

↓

Hit?

 ├── Yes → Return Cached Data

 └── No

        ↓

    MongoDB

        ↓

 Store in Redis

        ↓

 Return Response
```

### Advantages

* Easy to implement
* Cache only what's actually requested
* Saves memory
* Very popular in production systems

### Disadvantages

* First request is slower
* Cache misses increase database load temporarily

---

# Read-Through Cache

In this pattern:

The application asks the cache.

The cache itself is responsible for loading data from the database.

```text
Application

↓

Redis

↓

Database

↓

Redis

↓

Application
```

### Advantages

* Simpler application code

### Disadvantages

* Requires cache infrastructure that supports read-through behavior
* Not directly provided by Redis

---

# Write-Through Cache

When data is updated:

```text
Application

↓

Redis

↓

Database
```

Both cache and database are updated immediately.

Example:

Update Course Name

↓

Redis Updated

↓

MongoDB Updated

Advantages:

* Cache always stays fresh

Disadvantages:

* Slower write operations

---

# Write-Behind Cache

Application writes:

```text
Application

↓

Redis

↓

Immediate Response

↓

Background Worker

↓

MongoDB
```

Database update happens later.

Advantages:

* Very fast writes

Disadvantages:

* Risk of data loss if Redis fails before persistence

Used in:

* Analytics
* Logging
* Metrics collection

---

# Refresh-Ahead Cache

Instead of waiting for expiration,

Redis refreshes the cache **before TTL expires**.

Example

```text
TTL = 5 Minutes

4 Minutes 50 Seconds

↓

Background Refresh

↓

New Data Loaded

↓

TTL Reset
```

Advantages

* No cache miss
* Consistent low latency

Used in:

* Dashboards
* Popular reports
* Trending content

---

# When Should You Cache?

## 1. Frequently Read Data

Example

```http
GET /courses
```

Courses change only occasionally.

Excellent candidate for caching.

---

## 2. Expensive Database Queries

Example

```text
Dashboard Statistics

↓

10 Aggregation Queries

↓

2 Seconds
```

Cache the result.

---

## 3. Public Information

Examples:

* Categories
* Skills
* Course list
* Instructor list

Safe to cache.

---

## 4. Search Results

Example

```text
Search = Python
```

Many users search for the same keyword.

Cache search results for a short duration.

---

## 5. Reports

Example

```text
Top Courses

Weekly Statistics

Leaderboards
```

Ideal for caching.

---

# When NOT to Cache?

## 1. Frequently Changing Data

Example

```text
Live Student Attendance
```

Updates every few seconds.

Caching may serve stale data.

---

## 2. Payment Transactions

Never cache:

```text
Payment Status

Wallet Balance

Bank Transactions
```

Always read the latest data.

---

## 3. OTP Verification

Example

```text
123456
```

Must be validated in real time.

Although OTPs are often **stored in Redis**, they are **not cached API responses**. Redis is used here as a temporary data store with a short TTL.

---

## 4. Personalized Responses

Example

```http
GET /my-dashboard
```

Every user sees different data.

If cached incorrectly,

User A may receive User B's data.

---

## 5. Authentication APIs

Examples

```http
POST /login

POST /logout

POST /refresh-token
```

Never cache authentication responses.

---

## 6. Real-Time Data

Examples

* Live stock prices
* GPS locations
* Chat messages
* Live auction bids

Caching can make the information outdated.

---

# SkillHub API Caching Strategy

| API               | Cache?      | Reason                                                 |
| ----------------- | ----------- | ------------------------------------------------------ |
| GET /courses      | ✅ Yes       | Frequently accessed, rarely changes                    |
| GET /courses/{id} | ✅ Yes       | Individual course details                              |
| GET /skills       | ✅ Yes       | Reference data                                         |
| GET /categories   | ✅ Yes       | Rarely updated                                         |
| GET /dashboard    | ✅ Yes       | Expensive aggregations                                 |
| GET /leaderboard  | ✅ Yes       | Read-heavy                                             |
| GET /search       | ✅ Short TTL | Frequently repeated searches                           |
| POST /login       | ❌ No        | Authentication                                         |
| POST /logout      | ❌ No        | Authentication                                         |
| POST /payment     | ❌ No        | Financial data                                         |
| GET /my-profile   | ⚠ Depends   | User-specific, use user-specific cache keys if caching |

---

# Decision Flow

```text
Request

   │

Is it read-heavy?

   │

 ├── No → Don't Cache

 │

 └── Yes

      │

Changes frequently?

      │

 ├── Yes → Avoid Cache

 │

 └── No

      │

Expensive Query?

      │

 ├── No → Optional

 │

 └── Yes

      │

Cache It
```

---

# Production Best Practices

* ✅ Cache only data that benefits from it.
* ✅ Choose a caching pattern based on read/write behavior.
* ✅ Don't cache sensitive or financial information.
* ✅ Use short TTLs for data that changes more frequently.
* ✅ Always have a strategy for cache invalidation (covered in a later lesson).
* ✅ Measure performance before and after caching instead of assuming it helps.

---

# Interview Questions

### 1. What is a cache hit?

A cache hit occurs when the requested data is found in the cache and returned without querying the database.

---

### 2. What is a cache miss?

A cache miss occurs when the requested data is not present in the cache, requiring the application to fetch it from the database and optionally populate the cache.

---

### 3. Which caching pattern is most commonly used with Redis?

**Cache-Aside (Lazy Loading)** is the most common pattern because it is simple, efficient, and works well with Redis.

---

### 4. Should payment APIs be cached?

No. Payment-related data should always come from the authoritative data source to ensure correctness and avoid serving stale information.

---

### 5. Can OTPs be stored in Redis?

Yes. Redis is an excellent place to store OTPs with a short TTL. However, this is **temporary data storage**, not API response caching.

---

# Module Progress

* ✅ Lesson 1 — Redis Basics
* ✅ Lesson 2 — When to Cache vs When NOT to Cache

---

# Next Lesson

**Lesson 3 — Cache Key Design**

We'll implement a production-ready cache key strategy for the SkillHub API, covering:

* Naming conventions
* Prefix-based keys
* Versioned cache keys
* User-specific keys
* Pagination and search keys
* A reusable cache key builder utility that will be used throughout the rest of the module.
