# Module: Redis & Caching in FastAPI (Production Level)

# Lesson 3 — Cache Key Design (Production Ready)

> **Project:** SkillHub API

---

# Learning Objectives

By the end of this lesson, you will understand:

* What is a Cache Key?
* Why Cache Key Design is Important
* Cache Key Naming Conventions
* Prefix-based Design
* Resource-based Keys
* Versioned Cache Keys
* User-specific Cache Keys
* Pagination Cache Keys
* Search Cache Keys
* Multi-filter Cache Keys
* Multi-tenant Cache Keys
* Cache Key Builder Design
* Production Best Practices
* SkillHub Cache Key Architecture

---

# Before Learning Cache Keys

Suppose Redis contains:

```text
Python Course List
```

How does Redis know **which data** you're requesting?

Redis doesn't understand:

* Courses
* Students
* Skills
* Dashboards

Redis only understands

```text
KEY → VALUE
```

Example

```text
course:list

↓

[
   Python,
   FastAPI,
   React
]
```

The **key** uniquely identifies the cached data.

---

# What is a Cache Key?

A cache key is the **unique identifier** used to store and retrieve cached data.

Example

```text
course:list
```

↓

Value

```json
[
   {
      "id":1,
      "name":"Python"
   }
]
```

Whenever FastAPI needs the course list

↓

Redis searches

```text
course:list
```

---

# Redis Storage

Redis internally stores

```text
KEY                VALUE

course:list   →    [...]

student:5     →    {...}

dashboard      →    {...}
```

Everything begins with

```text
KEY
```

---

# Why Cache Key Design Matters

Imagine using

```text
courses
```

for everything.

Now cache

```text
Course List
```

Later

```text
Course Details
```

Same key.

Redis

↓

Old value overwritten.

Bad design causes

* Wrong responses
* Cache collisions
* Difficult debugging
* Impossible invalidation

---

# Good Cache Keys

A cache key should be

* Unique
* Predictable
* Readable
* Easy to invalidate
* Easy to debug

---

# Naming Convention

Industry standard

```text
resource:identifier
```

Examples

```text
course:list

course:15

student:20

dashboard

leaderboard
```

Notice

Everything starts with

```text
resource
```

---

# Why Use Colon (`:`)?

Instead of

```text
course_list
```

Use

```text
course:list
```

Reason

Redis CLI supports pattern matching.

Example

```bash
KEYS course:*
```

Output

```text
course:list

course:15

course:40

course:python
```

Much easier to manage.

---

# Prefix-based Design

Every resource gets a prefix.

Example

```text
course:

student:

skill:

dashboard:

leaderboard:
```

Example Redis

```text
course:list

course:15

student:5

student:10

skill:list

dashboard:admin
```

Much easier to organize.

---

# Resource Keys

Single Resource

```http
GET /courses/15
```

Cache

```text
course:15
```

---

Multiple Resources

```http
GET /courses
```

Cache

```text
course:list
```

---

Student

```http
GET /students/200
```

Cache

```text
student:200
```

---

# User-specific Cache Keys

Never use

```text
dashboard
```

Suppose

User A

↓

Caches dashboard

User B

↓

Requests dashboard

Redis returns

User A's dashboard

Huge security issue.

Instead

```text
user:15:dashboard

user:20:dashboard
```

Every user gets their own cache.

---

# Pagination Keys

API

```http
GET /courses?page=1
```

Cache

```text
course:list:page:1
```

---

Next

```http
GET /courses?page=2
```

Cache

```text
course:list:page:2
```

Each page becomes independent.

---

# Search Keys

Example

```http
GET /search?q=python
```

Cache

```text
search:python
```

---

Another

```http
GET /search?q=fastapi
```

Cache

```text
search:fastapi
```

---

# Multi-filter Keys

Suppose

```http
GET /courses

?category=python

&level=advanced

&language=english
```

Cache

```text
course:

category:python:

level:advanced:

language:english
```

Or

```text
course:python:advanced:english
```

Every filter combination gets its own key.

---

# Versioned Cache Keys

Sometimes

API changes.

Old cache

↓

Still exists.

Solution

```text
v1:course:list

v2:course:list
```

Old cache

↓

Ignored automatically.

Useful after

* Deployment
* API changes
* Response structure changes

---

# Multi-tenant Keys

Suppose

Company A

Company B

Both have

Student ID

```text
100
```

Wrong

```text
student:100
```

Correct

```text
tenant:companyA:student:100

tenant:companyB:student:100
```

Now

No collision.

---

# Dashboard Keys

Admin Dashboard

```text
dashboard:admin
```

Student Dashboard

```text
dashboard:student
```

Instructor Dashboard

```text
dashboard:instructor
```

Different dashboards

↓

Different cache.

---

# Leaderboard Keys

Weekly

```text
leaderboard:weekly
```

Monthly

```text
leaderboard:monthly
```

Yearly

```text
leaderboard:yearly
```

---

# Notification Keys

```text
user:25:notifications
```

Unread Count

```text
user:25:notification_count
```

---

# OTP Keys

```text
otp:9876543210
```

Email OTP

```text
otp:user@gmail.com
```

---

# Session Keys

```text
session:jwt_token
```

Or

```text
session:user:15
```

---

# Cache Key Hierarchy

```text
course

│

├── list

├── 10

├── 20

├── category:python

├── page:2

└── search:fastapi
```

---

# SkillHub Cache Architecture

```text
course:list

course:15

course:25

course:page:1

course:page:2

course:category:python

course:search:fastapi

student:10

student:50

student:10:profile

student:10:dashboard

skill:list

leaderboard:weekly

dashboard:admin
```

---

# Common Mistakes

## Bad

```text
data
```

Meaningless.

---

Bad

```text
cache1
```

Cannot identify.

---

Bad

```text
abc123
```

Impossible to debug.

---

Good

```text
course:list
```

Readable.

---

Good

```text
student:50
```

Clear.

---

Good

```text
user:20:dashboard
```

Self-explanatory.

---

# Cache Key Builder (Project Design)

Instead of manually writing keys everywhere:

❌ Bad

```python
redis.get("course:list")
redis.get("course:15")
redis.get("student:5")
```

Different developers may create different key formats, leading to inconsistencies.

---

## Better Approach

Create a centralized cache key builder.

```text
app/
│
├── core/
│   ├── cache_keys.py   ← New
│   ├── redis.py
│   └── config.py
│
├── services/
├── api/
└── ...
```

---

### Example Design

```python
class CacheKeys:

    @staticmethod
    def course_list():
        return "course:list"

    @staticmethod
    def course(course_id: str):
        return f"course:{course_id}"

    @staticmethod
    def student(student_id: str):
        return f"student:{student_id}"

    @staticmethod
    def student_dashboard(user_id: str):
        return f"user:{user_id}:dashboard"

    @staticmethod
    def course_page(page: int):
        return f"course:list:page:{page}"

    @staticmethod
    def search(query: str):
        return f"search:{query}"
```

### Why this is better

* One place to manage all cache keys.
* Consistent naming across the application.
* Easier refactoring.
* Simpler debugging and invalidation.
* Reduces typos and duplicate key formats.

---

# Production Best Practices

* ✅ Use colon-separated namespaces.
* ✅ Keep keys human-readable.
* ✅ Include identifiers where required.
* ✅ Include page/filter information in list queries.
* ✅ Version keys when response formats change.
* ✅ Centralize key generation in one utility.
* ✅ Never mix unrelated resources under the same key.

---

# Interview Questions

### 1. What is a cache key?

A cache key is the unique identifier used to store and retrieve a value from the cache.

---

### 2. Why are cache keys important?

They prevent collisions, make cache invalidation easier, improve debugging, and ensure the correct data is returned.

---

### 3. Why do Redis keys commonly use colons (`:`)?

Colons create namespaces, making keys organized and allowing pattern-based operations like `course:*`.

---

### 4. Why should user-specific data include the user ID in the cache key?

To prevent different users from sharing the same cached response and to avoid security and privacy issues.

---

### 5. Why should cache keys be generated through a helper class?

A centralized helper ensures consistent naming, reduces duplication, simplifies maintenance, and makes cache invalidation easier.

---

# Module Progress

* ✅ Lesson 1 — Redis Basics
* ✅ Lesson 2 — When to Cache vs When NOT to Cache
* ✅ Lesson 3 — Cache Key Design

---

# Next Lesson

**Lesson 4 — TTL (Time-To-Live)**

We'll cover:

* What TTL is and how Redis expiration works
* Absolute vs Sliding Expiration
* Choosing the right TTL for different types of data
* TTL strategies for the SkillHub API
* Implementing TTL with Redis in FastAPI
* Monitoring and debugging key expiration in production
