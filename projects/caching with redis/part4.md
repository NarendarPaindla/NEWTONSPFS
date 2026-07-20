# Module: Redis & Caching in FastAPI (Production Level)

# Lesson 4 — TTL (Time-To-Live) (Production Ready)

> **Project:** SkillHub API

---

# Learning Objectives

By the end of this lesson, you will understand:

* What is TTL (Time-To-Live)?
* Why TTL is needed
* How Redis Expiration works
* Absolute Expiration
* Sliding Expiration
* Permanent Cache
* Choosing the Right TTL
* TTL Strategies
* TTL for Different Types of Data
* Common TTL Mistakes
* Production TTL Best Practices
* SkillHub API TTL Design

---

# What is TTL?

TTL stands for **Time-To-Live**.

It defines **how long a cached item should remain in Redis before it expires automatically**.

Example:

```text
course:list

↓

TTL = 600 Seconds

↓

10 Minutes Later

↓

Automatically Deleted
```

Redis removes the key after the TTL expires.

---

# Why Do We Need TTL?

Imagine Redis without TTL.

```text
course:list

↓

Stored Forever
```

Now suppose the administrator updates a course.

Database:

```text
Python → Python 3.14
```

Redis still contains:

```text
Python
```

Users continue receiving outdated data.

This is called **stale data**.

TTL helps reduce the amount of stale data by automatically expiring cached entries.

> **Note:** TTL alone does not guarantee fresh data. For critical updates, cache invalidation (covered in the next lessons) is also required.

---

# Without TTL

```text
Database

↓

Update

↓

Redis

↓

Old Data

↓

Users
```

Users receive outdated information until the cache is manually cleared.

---

# With TTL

```text
Redis

↓

TTL Expires

↓

Key Removed

↓

Next Request

↓

MongoDB

↓

Fresh Data

↓

Redis
```

Fresh data replaces the expired cache.

---

# Redis Expiration Lifecycle

```text
First Request

↓

MongoDB

↓

Store in Redis

↓

TTL Starts

↓

Client Requests

↓

TTL Ends

↓

Redis Deletes Key

↓

Next Request

↓

MongoDB

↓

Store Again
```

---

# Example

Course List

```text
TTL = 300 Seconds
```

Timeline

```text
10:00 AM

↓

Data Cached

↓

10:04 AM

↓

Redis Hit

↓

10:05 AM

↓

TTL Expired

↓

10:06 AM

↓

Database Query

↓

New Cache Created
```

---

# Absolute Expiration

The cache expires after a **fixed amount of time**, regardless of how many times it is accessed.

Example:

```text
Cache Created

↓

TTL = 10 Minutes

↓

10 Minutes

↓

Deleted
```

Even if 10,000 users access the key during those 10 minutes, it still expires at the scheduled time.

### Redis Default Behavior

Redis uses **absolute expiration** by default.

---

# Sliding Expiration

Sliding expiration resets the expiration timer every time the key is accessed.

Example:

```text
10:00

↓

Cache Created

↓

TTL = 10 Minutes

↓

10:08

↓

User Access

↓

TTL Reset

↓

Expires at 10:18
```

Every access extends the lifetime.

---

# Does Redis Support Sliding Expiration?

**Not automatically.**

Redis only supports **absolute expiration** natively.

If you want sliding expiration, your application must explicitly extend the TTL whenever the key is accessed.

---

# Permanent Cache

Redis also allows keys without expiration.

Example:

```text
SET country:list
```

No TTL.

The key remains until:

* Deleted manually
* Redis is flushed
* Memory policies remove it (depending on configuration)

Use permanent caches carefully.

---

# Choosing the Right TTL

Choosing TTL is a balance between:

* Data freshness
* Performance
* Database load

Short TTL

```text
Fresh Data

↓

More Database Queries
```

Long TTL

```text
Less Database Load

↓

Higher Risk of Stale Data
```

---

# SkillHub TTL Strategy

## Course List

Changes:

Few times per day

Recommended TTL

```text
30 Minutes
```

---

## Course Details

Changes:

Occasionally

TTL

```text
15 Minutes
```

---

## Skills

Almost never change.

TTL

```text
24 Hours
```

---

## Categories

Rarely updated.

TTL

```text
24 Hours
```

---

## Dashboard Statistics

Frequently updated.

TTL

```text
5 Minutes
```

---

## Search Results

Highly dynamic.

TTL

```text
10 Minutes
```

---

## Leaderboard

Changes regularly.

TTL

```text
2 Minutes
```

---

## User Dashboard

Personalized.

TTL

```text
1 Minute
```

or

No caching depending on requirements.

---

# Recommended TTL Table

| Resource       | Suggested TTL        |
| -------------- | -------------------- |
| Categories     | 24 Hours             |
| Skills         | 24 Hours             |
| Course List    | 30 Minutes           |
| Course Details | 15 Minutes           |
| Search Results | 10 Minutes           |
| Dashboard      | 5 Minutes            |
| Leaderboard    | 2 Minutes            |
| User Dashboard | 1 Minute (if cached) |

> These values are examples. The appropriate TTL depends on how often the underlying data changes and how fresh it needs to be for your users.

---

# Real-World Examples

## Netflix

Movie catalog

```text
Long TTL
```

Catalog changes relatively infrequently.

---

## Amazon

Product Details

```text
Medium TTL
```

Prices and availability may change throughout the day.

---

## Instagram

Trending Feed

```text
Very Short TTL
```

Content popularity changes rapidly.

---

## Banking

Account Balance

```text
Usually No Response Cache
```

Users expect the latest balance.

---

# Common TTL Mistakes

## Very Long TTL

```text
7 Days
```

Course updated yesterday.

Users still see the old course.

---

## Very Short TTL

```text
5 Seconds
```

Almost every request becomes a cache miss.

Little benefit from caching.

---

## No TTL Everywhere

Memory keeps growing.

Eventually Redis may evict keys based on its configured eviction policy or run out of available memory.

---

# TTL and Memory

Imagine

```text
500,000 Cached Keys
```

Without expiration

↓

Memory usage keeps increasing.

With TTL

↓

Old entries are automatically removed.

TTL helps Redis reclaim memory from outdated cached data.

---

# TTL vs Manual Invalidation

TTL

```text
Wait

↓

Expire

↓

Fresh Data
```

Manual Invalidation

```text
Database Updated

↓

Delete Cache Immediately

↓

Next Request Builds Fresh Cache
```

Production systems often use **both**:

* TTL as a safety net.
* Manual invalidation for important updates.

---

# How TTL Fits into Cache-Aside

```text
Client

↓

Redis

↓

Hit?

│

├── Yes

│      ↓

│  Return Data

│

└── No

      ↓

MongoDB

      ↓

Redis (Set TTL)

      ↓

Return Response
```

Every newly cached value receives an expiration time.

---

# Production Best Practices

* ✅ Choose TTL based on business requirements, not arbitrary values.
* ✅ Use shorter TTLs for frequently changing data.
* ✅ Use longer TTLs for reference/master data.
* ✅ Combine TTL with cache invalidation.
* ✅ Monitor cache hit ratio and adjust TTL based on real usage.
* ✅ Avoid making every endpoint use the same TTL.

---

# Interview Questions

### 1. What is TTL?

TTL (Time-To-Live) is the duration for which a cached key remains valid before Redis automatically expires it.

---

### 2. Why is TTL important?

TTL prevents cached data from remaining indefinitely, reducing stale data and freeing memory over time.

---

### 3. Does Redis support sliding expiration by default?

No. Redis supports absolute expiration by default. Sliding expiration must be implemented in the application.

---

### 4. Should all cached data use the same TTL?

No. TTL should be selected based on how frequently the underlying data changes and the application's freshness requirements.

---

### 5. Is TTL alone enough to keep cached data fresh?

No. Important updates often require explicit cache invalidation in addition to TTL.

---

# Module Progress

* ✅ Lesson 1 — Redis Basics
* ✅ Lesson 2 — When to Cache vs When NOT to Cache
* ✅ Lesson 3 — Cache Key Design
* ✅ Lesson 4 — TTL (Time-To-Live)

---

# Next Lesson

**Lesson 5 — FastAPI Integration with `fastapi-cache2`**

In this lesson, we'll begin the **hands-on implementation** in the SkillHub project by:

* Installing `fastapi-cache2`
* Configuring Redis as the cache backend
* Initializing caching during FastAPI startup
* Creating reusable cache configuration
* Caching real API endpoints such as `GET /courses`
* Measuring the performance improvement before and after caching using complete project files.
