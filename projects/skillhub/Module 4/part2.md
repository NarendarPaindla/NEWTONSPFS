Excellent. Before we continue, I want to make one architectural decision that will significantly improve the rest of the course.

## We are going to restart Module 4 from an Industry Standard Architecture.

**Reason:**

The code we wrote in Module 3 was good for learning CRUD, but before adding Users, Authentication, JWT, Comments, etc., we should fix the architecture.

For example, currently our code has issues like:

* API directly calling Service
* Service returning raw MongoDB results
* No Dependency Injection
* No Repository Interfaces
* No Generic Response Models
* No Timestamp fields
* No Base Models
* No Configurable MongoDB collections

If we continue building on this, we'll have to rewrite many files during Authentication.

So we'll refactor **once**, then continue building professionally.

---

# Module 4 — Part 2

# Production Refactoring (Industry Standard)

## Goal

After this module, our architecture will be:

```text
Client
    │
    ▼
Router
    │
    ▼
Dependency Injection
    │
    ▼
Service
    │
    ▼
Repository
    │
    ▼
MongoDB
```

This is the architecture we'll use for the rest of the course.

---

# Step 1

## Updated Project Structure

```text
skillhub-api/
│
├── app/
│
├── api/
│   ├── skills.py
│   ├── users.py
│
├── core/
│   ├── config.py
│
├── db/
│   ├── database.py
│
├── dependencies/
│   ├── skill_dependencies.py
│   └── user_dependencies.py
│
├── models/
│
├── repositories/
│   ├── skill_repository.py
│   └── user_repository.py
│
├── schemas/
│   ├── common_schema.py     ⭐ NEW
│   ├── skill_schema.py
│   └── user_schema.py
│
├── services/
│   ├── skill_service.py
│   └── user_service.py
│
├── utils/
│
├── exceptions/
│
└── main.py
```

---

# Step 2

## NEW FILE

```text
app/schemas/common_schema.py
```

This file will be used everywhere.

---

## COMPLETE CODE

```python
from datetime import datetime

from pydantic import BaseModel


class TimestampSchema(BaseModel):

    created_at: datetime

    updated_at: datetime
```

---

## Why?

Every collection in our application will have

* Users
* Skills
* Comments
* Bookmarks
* Categories

All of them need

```text
created_at

updated_at
```

Instead of repeating

```python
created_at

updated_at
```

inside every schema,

we inherit.

---

Example later

```python
class SkillResponse(
    TimestampSchema
)
```

Automatically gets

```text
created_at

updated_at
```

---

# Step 3

## Update Skill Document

From now on

Every Skill document becomes

```json
{
    "_id":"...",

    "name":"Python",

    "category":"Backend",

    "level":"Advanced",

    "experience":2,

    "user_id":"...", ⭐

    "created_at":"2026-07-14T10:30:00",

    "updated_at":"2026-07-14T10:30:00"
}
```

Notice

Now

Every Skill has

* Owner
* Created Time
* Updated Time

Exactly like real applications.

---

# Step 4

## Repository Responsibility Changes

Current Repository

```text
Insert

Read

Update

Delete
```

Good.

But

Repository should NEVER

* Validate data
* Check duplicates
* Raise HTTPException

Repository should ONLY

communicate with MongoDB.

Very important interview point.

---

# Step 5

## Service Responsibility

Service should now handle

```text
Duplicate Check

↓

Business Validation

↓

Owner Validation

↓

Timestamp Creation

↓

Repository
```

The Service becomes the "brain".

---

# Step 6

## Dependency Injection

Instead of

```python
skill_service
```

being imported everywhere,

later we'll use

```python
Depends(
    get_skill_service
)
```

Exactly like

FastAPI's

```python
Depends(get_current_user)
```

during Authentication.

So we are preparing for JWT.

---

# Step 7

## Better API Responses

Current

```json
{
"success":true
}
```

Later every API will return

```json
{
    "success":true,

    "message":"Skill Created",

    "data":{

    },

    "meta":{

    }
}
```

This format will never change again.

---

# Step 8

## MongoDB Collections

Instead of

```python
database.skills
```

everywhere,

We'll create

```python
database["skills"]
```

Reason

Later

Collection names can come from

```text
.env
```

Useful for

* Testing
* Development
* Production

---

# Step 9

## Users

Now we're finally ready.

Next lesson

We'll build

```
Users
```

professionally.

Including

```
Repository

↓

Service

↓

Dependency

↓

API

↓

Response Model

↓

Serializer

↓

Timestamps

↓

Relationship Ready
```

---

# Course Update

From now on, **every lesson will follow this exact format**:

## 1. Updated Project Structure

(Complete tree)

---

## 2. New Files

Example

```
Create

app/repositories/user_repository.py
```

---

## 3. Updated Files

Example

```
Update

app/api/users.py
```

---

## 4. Complete Code

**Entire file**

Not snippets.

---

## 5. Line-by-Line Explanation

Every new concept explained.

---

## 6. Flow Diagram

```
Client

↓

Router

↓

Service

↓

Repository

↓

MongoDB
```

---

## 7. Swagger Testing

Request

Response

Expected MongoDB Output

---

## 8. Interview Questions

---

## 9. Assignments

---

## 10. Mini Challenge

---

## 11. Summary

---

# ⚠️ Important Note

At this point, because we've changed the teaching format, the next lesson (**Users Module**) will require updating **multiple existing files** (`main.py`, schemas, repositories, services, APIs, etc.).

That means the response will be **too large to fit in a single ChatGPT message**.

So starting with the next lesson, I'll split it like this:

* **Part A:** Project structure + all new/updated schemas + complete code.
* **Part B:** Repositories + Services + complete code.
* **Part C:** APIs + `main.py` updates + testing + explanations.

This way, you'll always receive **complete runnable files** without missing any code or having to guess where changes belong. This is much closer to how a real project evolves.
