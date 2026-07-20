# Module: Redis & Caching (Production Level)

# Lesson 7 — Cache Stampede (Production Scalability)

> **Project:** SkillHub API

This is one of the most important Redis concepts asked in **backend interviews (2–8 years experience)** and is critical for building scalable systems.

---

# Learning Objectives

By the end of this lesson, you'll understand:

* What is Cache Stampede?
* Dogpile Effect
* Cache Breakdown
* Cache Avalanche
* Cache Penetration
* Hot Keys
* Distributed Lock (Mutex)
* Request Coalescing
* Random TTL
* Background Refresh
* Bloom Filter
* Hot Key Protection
* Production Strategies used by Netflix, Amazon, Instagram, and YouTube

---

# Imagine Our SkillHub API

We have one endpoint.

```http
GET /courses
```

Normally

```text
Client

↓

Redis

↓

Return Data
```

Everything is fast.

---

# Now Imagine

The cache expires.

Exactly at

```
10:00:00
```

At that exact second

```
50,000 Students
```

refresh the page.

Now Redis contains

```
Nothing
```

Every request becomes

```
Cache Miss
```

---

# What Happens?

Instead of

```
Redis

↓

Client
```

Now

```text
50,000 Requests

↓

MongoDB

↓

50,000 Queries
```

The database suddenly receives an enormous spike in traffic.

This is called a **Cache Stampede**.

---

# Definition

A **Cache Stampede** occurs when many requests simultaneously miss the cache and all try to rebuild it from the database.

---

# Normal Flow

```text
Client

↓

Redis

↓

Hit

↓

Client
```

---

# During Stampede

```text
50,000 Clients

↓

Redis

↓

Miss

↓

MongoDB

↓

50,000 Queries
```

Database becomes overloaded.

---

# Real Example

Suppose

```
GET /courses
```

takes

```
300 ms
```

Normally

```
Redis

↓

2 ms
```

When cache expires

```
300 ms × 50,000 Requests
```

The database can become saturated, leading to slow responses or failures.

---

# Dogpile Effect

The **Dogpile Effect** is another name for Cache Stampede.

Imagine:

One biscuit.

Fifty dogs.

Everyone jumps at once.

Exactly the same happens when thousands of requests try to rebuild the same cache entry.

---

# Cache Breakdown

Suppose one very popular key

```
course:list
```

expires.

Every request needs

```
course:list
```

Result

```
Huge Database Load
```

Only one key caused the issue.

This is called **Cache Breakdown**.

---

# Cache Avalanche

Now imagine

```
100,000 Keys
```

All created at

```
9:00 AM
```

TTL

```
30 Minutes
```

At

```
9:30 AM
```

Every key expires.

Result

```
Redis Empty

↓

Millions of Database Queries
```

This is a **Cache Avalanche**.

---

# Cache Penetration

Suppose someone requests

```
Course ID

999999999
```

It doesn't exist.

Redis

↓

Miss

MongoDB

↓

Not Found

Next request

↓

MongoDB again

This repeats indefinitely because nothing is ever cached.

Attackers can exploit this behavior.

---

# Hot Keys

Some cache keys receive much more traffic than others.

Example

```
course:list
```

```
dashboard
```

```
homepage
```

These are called **Hot Keys**.

When they expire, the impact is much larger than for ordinary keys.

---

# Solution 1 — Distributed Lock (Mutex)

Only **one request** is allowed to rebuild the cache.

Example

```text
50,000 Requests

↓

Redis

↓

Miss

↓

Acquire Lock

↓

Only One Request

↓

MongoDB

↓

Redis Updated

↓

Release Lock

↓

Remaining Requests Read Cache
```

This is the most common production solution.

---

# Example Flow

Without Lock

```text
50,000 Requests

↓

50,000 MongoDB Queries
```

With Lock

```text
50,000 Requests

↓

1 MongoDB Query

↓

49,999 Redis Reads
```

Huge improvement.

---

# Solution 2 — Request Coalescing

Multiple requests asking for the same resource are grouped together.

```text
Request A

Request B

Request C

↓

One Database Query

↓

Shared Result
```

Instead of executing the same work multiple times, one request performs it and the others reuse the result.

---

# Solution 3 — Random TTL

Suppose every key uses

```
30 Minutes
```

Then they all expire together.

Instead

```
28 Minutes

31 Minutes

35 Minutes

27 Minutes

33 Minutes
```

Now expirations are spread over time.

This greatly reduces the risk of a cache avalanche.

---

# Example

Bad

```
course:1

30 Minutes
```

```
course:2

30 Minutes
```

```
course:3

30 Minutes
```

Everything expires simultaneously.

---

Better

```
course:1

28 Minutes
```

```
course:2

31 Minutes
```

```
course:3

34 Minutes
```

Expiration becomes staggered.

---

# Solution 4 — Background Refresh

Instead of waiting for expiration

```
TTL

↓

Almost Expired

↓

Background Job

↓

Refresh Cache

↓

Reset TTL
```

Users continue receiving cached data while the refresh happens in the background.

---

# Solution 5 — Never Expire Hot Keys

For extremely popular data

```
Homepage

Course List

Categories
```

Some systems choose not to let the cache expire automatically.

Instead

```
Admin Updates

↓

Delete Cache

↓

Next Request Rebuilds
```

This relies on explicit invalidation rather than TTL.

---

# Solution 6 — Bloom Filter

Suppose

```
Student ID

999999999
```

doesn't exist.

Without protection

```text
Redis

↓

Miss

↓

MongoDB

↓

Not Found
```

Repeated endlessly.

A **Bloom Filter** is a probabilistic data structure that quickly checks whether an item is **definitely not present** or **possibly present**.

Flow

```text
Request

↓

Bloom Filter

↓

Definitely Not Present?

↓

Yes

↓

Reject Immediately
```

Database is never queried for obviously invalid IDs.

> **Note:** A Bloom Filter can have **false positives** (it may say an item *might* exist when it doesn't), but it does **not** produce false negatives.

---

# Solution 7 — Cache Empty Results

Suppose

```
Course ID

999999
```

doesn't exist.

Instead of caching nothing,

store

```
NULL
```

with a short TTL.

Next request

↓

Redis

↓

NULL

↓

No Database Query

This is called **Negative Caching**.

---

# Real Industry Strategies

## Netflix

* Background refresh
* Distributed locking
* Long-lived caches for metadata

---

## Amazon

* Random TTL
* Distributed locks
* Hot key monitoring

---

## Instagram

* Heavy Redis usage
* Feed precomputation
* Background cache refresh

---

## YouTube

* Cache videos
* Cache recommendations
* Multiple cache layers
* Intelligent invalidation

---

# Which Strategy Should SkillHub Use?

| Problem           | Solution                       |
| ----------------- | ------------------------------ |
| Cache Stampede    | Distributed Lock               |
| Cache Breakdown   | Distributed Lock               |
| Cache Avalanche   | Random TTL                     |
| Cache Penetration | Bloom Filter + Negative Cache  |
| Hot Keys          | Manual Invalidation + Long TTL |
| Dashboard         | Background Refresh             |
| Search Results    | Short TTL + Negative Cache     |

---

# Decision Flow

```text
Cache Miss

      │

Is Another Request Rebuilding?

      │

 ├── Yes

 │      │

 │  Wait / Use Existing Result

 │

 └── No

        │

   Acquire Lock

        │

 Query Database

        │

 Update Redis

        │

 Release Lock
```

---

# Production Best Practices

* ✅ Never let thousands of requests rebuild the same cache simultaneously.
* ✅ Use distributed locks for expensive cache rebuilds.
* ✅ Randomize TTL values for large groups of keys.
* ✅ Cache negative results for frequently requested invalid resources.
* ✅ Monitor hot keys and treat them differently from ordinary keys.
* ✅ Use background refresh for critical, frequently accessed data.

---

# Interview Questions

### 1. What is a Cache Stampede?

A Cache Stampede occurs when many requests simultaneously miss the cache and all try to rebuild it from the database, causing a surge in database traffic.

---

### 2. What is the difference between Cache Breakdown and Cache Avalanche?

* **Cache Breakdown:** One highly popular key expires, causing many requests for that single key.
* **Cache Avalanche:** Many cache keys expire around the same time, causing widespread cache misses.

---

### 3. What is the most common solution for a Cache Stampede?

Using a **distributed lock (mutex)** so that only one request rebuilds the cache while others wait or reuse the rebuilt result.

---

### 4. Why use Random TTL?

Random TTL prevents many keys from expiring simultaneously, reducing the chance of a cache avalanche.

---

### 5. What is Negative Caching?

Negative caching stores "not found" results (often with a short TTL) to avoid repeatedly querying the database for invalid requests.

---

# Redis & Caching Module Completed ✅

You have now completed the entire Redis module:

* ✅ Redis Basics
* ✅ When to Cache vs When NOT to Cache
* ✅ Cache Key Design
* ✅ TTL
* ✅ FastAPI Integration with `fastapi-cache2`
* ✅ Cache Invalidation Patterns
* ✅ Cache Stampede

By completing these lessons, you've covered the Redis caching concepts commonly expected in production FastAPI applications and backend interviews.
