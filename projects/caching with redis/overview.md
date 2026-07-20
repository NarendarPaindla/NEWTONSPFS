Good catch. Since your syllabus contains **only these seven subtopics**, the roadmap should be focused and deep instead of expanding into additional Redis topics.

# Module: Redis & Caching in FastAPI (Production Level)

**Project:** SkillHub API (Production-Ready Implementation)

Throughout this module, we'll enhance the existing SkillHub API by integrating Redis caching to improve performance, scalability, and reliability using real-world caching patterns.

---

# Lesson 1 — Redis Basics

## Topics

* What is Redis?
* Why Redis?
* Redis Architecture
* In-Memory Data Store
* Redis Installation
* Redis Server & Client
* Redis CLI
* Redis Data Types (Overview)
* How Redis Works Internally
* Advantages & Limitations
* Real-World Redis Use Cases

### Project Implementation

Build Redis connectivity for SkillHub API.

* Install Redis
* Configure Redis server
* Connect FastAPI to Redis
* Create a Redis connection manager
* Verify connection with a health check endpoint
* Store and retrieve sample data from Redis

**Outcome:** FastAPI successfully communicates with Redis.

---

# Lesson 2 — When to Cache vs When NOT to Cache

## Topics

### When to Cache

* Frequently accessed data
* Read-heavy APIs
* Dashboard statistics
* Search results
* Public data
* Reference/master data
* Expensive database queries

### When NOT to Cache

* Frequently changing data
* Sensitive information
* OTPs
* Payment transactions
* Real-time stock prices
* Live chat messages
* Personalized responses (unless carefully designed)

### Caching Patterns

* Cache-Aside (Lazy Loading)
* Read-Through
* Write-Through
* Write-Behind
* Refresh-Ahead

### Project Implementation

Analyze the SkillHub API and decide which endpoints should be cached.

Examples:

* Cache Course List
* Cache Skill Categories
* Cache Instructor Profiles
* Do **not** cache Login, OTP Verification, or Payment APIs

**Outcome:** A production-ready caching strategy for the project.

---

# Lesson 3 — Cache Key Design

## Topics

* Why cache keys matter
* Naming conventions
* Prefix-based design
* Resource-based keys
* Versioned keys
* User-specific keys
* Pagination keys
* Filter/Search keys
* Multi-tenant key design

Example Keys

```text
course:list

course:15

course:v1:list

user:25:profile

user:25:courses

search:python

dashboard:admin

student:100:skills

course:list:page:2
```

### Project Implementation

Design a scalable cache key convention for the SkillHub API and implement reusable cache key helper functions.

**Outcome:** Consistent, maintainable cache keys across the application.

---

# Lesson 4 — TTL (Time-To-Live)

## Topics

* What is TTL?
* Absolute Expiration
* Sliding Expiration
* Permanent Cache
* Choosing the Right TTL
* Balancing Freshness vs Performance
* Memory Considerations

Recommended TTL Examples

* Course List → 30 minutes
* Dashboard → 5 minutes
* Instructor Profile → 1 hour
* Search Results → 10 minutes
* Categories → 24 hours

### Project Implementation

Apply different TTL values to different SkillHub endpoints based on business requirements.

**Outcome:** Intelligent expiration policies instead of a one-size-fits-all approach.

---

# Lesson 5 — FastAPI Integration with `fastapi-cache2`

## Topics

* Installing `fastapi-cache2`
* Redis Backend
* Cache Initialization
* Dependency Injection
* Cache Decorator
* Response Serialization
* Namespaces
* Custom Key Builders
* Manual Cache Operations

### Project Implementation

Integrate Redis caching into the SkillHub API.

Cache APIs such as:

* `GET /courses`
* `GET /courses/{id}`
* `GET /skills`
* `GET /dashboard`

Compare performance before and after caching.

**Outcome:** Production-ready FastAPI + Redis integration using `fastapi-cache2`.

---

# Lesson 6 — Cache Invalidation Patterns

## Topics

* Why cache invalidation is difficult
* Delete-on-Update
* Update-on-Write
* Versioned Cache
* Event-Driven Invalidation
* Bulk Invalidation
* Namespace Invalidation
* TTL-based Invalidation
* Cache Consistency

### Project Implementation

When a course is updated:

1. Update the database
2. Invalidate the corresponding cache entry
3. Allow the next request to rebuild the cache

Implement invalidation for:

* Courses
* Skills
* Instructor Profiles
* Dashboard Statistics

**Outcome:** Cached data remains consistent with the database.

---

# Lesson 7 — Cache Stampede

## Topics

* What is Cache Stampede?
* Dogpile Effect
* Cache Breakdown
* Cache Avalanche
* Cache Penetration
* Why these problems occur
* Prevention Strategies

### Solutions

* Distributed Locks
* Mutex Locking
* Randomized TTL (Jitter)
* Background Refresh
* Request Coalescing
* Bloom Filters (Concept)
* Hot Key Protection

### Project Implementation

Protect the SkillHub Dashboard API from cache stampedes.

Simulate multiple concurrent requests and implement a locking strategy so only one request rebuilds the cache while others wait for the refreshed value.

**Outcome:** A highly scalable, production-ready caching layer capable of handling heavy traffic without overwhelming the database.

---

# Final Project Outcome

By the end of this module, the SkillHub API will have:

* ✅ Redis integrated with FastAPI
* ✅ Well-designed cache keys
* ✅ Intelligent TTL strategies
* ✅ Automatic response caching with `fastapi-cache2`
* ✅ Robust cache invalidation
* ✅ Protection against cache stampedes
* ✅ Performance improvements using industry-standard caching patterns

This revised roadmap aligns exactly with your syllabus while ensuring each lesson includes **hands-on implementation in the SkillHub project** and follows production best practices.
