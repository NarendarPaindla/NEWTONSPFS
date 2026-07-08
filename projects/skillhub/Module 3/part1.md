# Backend Development with FastAPI + MongoDB

# **Module 3 — Part 1**

# **Creating Our First Collection – Skills API (POST)**

> **Today is a major milestone.**
>
> Until now, our application could connect to MongoDB.
>
> **Today, we'll save real data into the database for the first time.**

---

# 1. Learning Objectives

By the end of this lesson, you will be able to:

* Understand MongoDB Collections and Documents.
* Design a `Skill` document.
* Understand Pydantic Models.
* Create Request Schemas.
* Create Response Schemas.
* Create your first POST API.
* Insert data into MongoDB using Motor.
* Validate incoming data.
* Return proper HTTP status codes.
* Test APIs using Swagger.

---

# 2. Before Writing Code

Let's think like software engineers.

Our application is **SkillHub**.

A user logs in and adds a skill.

Example:

```text
Python
Experience : 2 Years
Level : Advanced
Category : Backend
```

Where should this be stored?

Inside MongoDB.

MongoDB stores data inside **Collections**.

We'll create our first collection.

```
skills
```

---

# 3. Understanding MongoDB Collection

```
skillhub_db
        │
        ▼
   skills Collection
        │
        ▼
 ┌────────────────────┐
 │ Skill Document 1   │
 ├────────────────────┤
 │ Skill Document 2   │
 ├────────────────────┤
 │ Skill Document 3   │
 └────────────────────┘
```

Every document represents **one skill**.

---

# 4. Designing the Skill Document

Before writing code, always design your database.

Our first version:

```json
{
    "_id": ObjectId("..."),
    "name": "Python",
    "category": "Backend",
    "level": "Advanced",
    "experience": 2
}
```

Let's understand each field.

| Field      | Meaning                            |
| ---------- | ---------------------------------- |
| _id        | Generated automatically by MongoDB |
| name       | Skill Name                         |
| category   | Backend / Frontend / Database      |
| level      | Beginner / Intermediate / Advanced |
| experience | Years of Experience                |

Simple.

We'll improve this document later.

---

# 5. Why Don't We Accept Raw JSON?

Suppose a client sends:

```json
{
    "skill": "Python"
}
```

Another client sends

```json
{
    "name": "Python"
}
```

Another client sends

```json
{
    "Name": "Python"
}
```

Another client sends

```json
{
    "language": "Python"
}
```

Which one is correct?

Chaos.

We need rules.

---

# 6. Pydantic Solves This Problem

Pydantic validates incoming requests.

Instead of accepting anything,

we define exactly what we expect.

---

# 7. Create Folder

```
app/
    schemas/
```

Inside create

```
skill_schema.py
```

---

# 8. Create Request Schema

**File**

```
app/schemas/skill_schema.py
```

```python
from pydantic import BaseModel, Field


class SkillCreate(BaseModel):
    name: str = Field(..., min_length=2, max_length=50)

    category: str = Field(..., min_length=2)

    level: str = Field(...)

    experience: int = Field(..., ge=0)
```

---

# 9. Line-by-Line Explanation

Import

```python
from pydantic import BaseModel, Field
```

`BaseModel`

↓

Creates validation models.

`Field`

↓

Adds validation rules.

---

### SkillCreate

```python
class SkillCreate(BaseModel):
```

Represents the request body.

Example request:

```json
{
    "name": "Python",
    "category": "Backend",
    "level": "Advanced",
    "experience": 2
}
```

---

### Name Validation

```python
name: str = Field(..., min_length=2)
```

Rules:

* Required
* String
* Minimum 2 characters

Valid

```
Python
```

Invalid

```
P
```

---

### Experience Validation

```python
experience: int = Field(..., ge=0)
```

`ge`

means

Greater than or Equal.

Valid

```
0

2

10
```

Invalid

```
-5
```

---

# 10. Why Validation Matters

Without validation

Someone could send

```json
{
    "experience": -100
}
```

Does that make sense?

No.

Backend should reject invalid data.

---

# 11. Create Response Schema

Below the previous class.

```python
class SkillResponse(BaseModel):
    id: str

    name: str

    category: str

    level: str

    experience: int
```

Why separate Request and Response?

Because later we may return additional fields like:

* created_at
* updated_at
* user_id

without asking clients to send them.

---

# 12. Folder Structure

```
app/

├── schemas/
│      skill_schema.py
```

---

# 13. Create API Folder

```
app/

api/

    skills.py
```

---

# 14. First Skills API

```python
from fastapi import APIRouter, status

from app.db.database import database

from app.schemas.skill_schema import SkillCreate

router = APIRouter(prefix="/skills", tags=["Skills"])


@router.post(
    "/",
    status_code=status.HTTP_201_CREATED,
)
async def create_skill(skill: SkillCreate):

    result = await database.skills.insert_one(
        skill.model_dump()
    )

    return {
        "message": "Skill Created Successfully",
        "id": str(result.inserted_id)
    }
```

---

# 15. Understanding Every Line

## Router

```python
router = APIRouter(
```

Instead of putting every API in

```
main.py
```

we organize APIs into separate routers.

Professional projects may have dozens of routers.

---

### Prefix

```
/skills
```

Every endpoint starts with

```
/skills
```

---

### Tags

Swagger groups APIs under

```
Skills
```

making documentation easier to navigate.

---

### Request Body

```python
skill: SkillCreate
```

FastAPI automatically:

* Reads JSON
* Validates it
* Converts it into a Python object

---

### model_dump()

```python
skill.model_dump()
```

Pydantic v2 converts the model into a plain Python dictionary.

Example

```python
{
'name':'Python',
'category':'Backend',
'level':'Advanced',
'experience':2
}
```

Motor expects a dictionary.

---

### insert_one()

```python
await database.skills.insert_one(...)
```

This creates the `skills` collection automatically if it doesn't already exist and inserts the new document.

The result contains metadata, including the generated `_id`.

---

# 16. Register Router

Update

```
main.py
```

```python
from app.api.skills import router as skill_router

app.include_router(skill_router)
```

Your application now knows about the Skills API.

---

# 17. Complete Flow

```
Client

↓

POST /skills

↓

FastAPI

↓

Validation

↓

Motor

↓

MongoDB

↓

skills Collection

↓

Document Saved

↓

Success Response
```

This is the complete request lifecycle.

---

# 18. Run Server

```
uvicorn app.main:app --reload
```

Open Swagger

```
http://127.0.0.1:8000/docs
```

You should see

```
Skills
```

Click

```
POST /skills
```

---

# 19. Test Request

```json
{
  "name": "Python",
  "category": "Backend",
  "level": "Advanced",
  "experience": 2
}
```

Execute.

---

# 20. Expected Response

Status Code

```
201 Created
```

Body

```json
{
    "message": "Skill Created Successfully",
    "id": "684fa2..."
}
```

Notice that MongoDB generates a unique `_id` for every document.

---

# 21. Verify in MongoDB

If you open MongoDB Compass, you'll see:

```json
{
    "_id": ObjectId("684fa2..."),
    "name": "Python",
    "category": "Backend",
    "level": "Advanced",
    "experience": 2
}
```

The `skills` collection is created automatically on the first insert.

---

# 22. Common Errors

### Error

```
422 Unprocessable Entity
```

Reason

Validation failed.

Example

```json
{
"name":"P"
}
```

Missing required fields or failing validation rules.

---

### Error

```
No module named schemas
```

Reason

Incorrect import path.

Use

```python
from app.schemas.skill_schema import SkillCreate
```

---

### Error

```
ObjectId is not JSON serializable
```

Reason

MongoDB's `_id` is an `ObjectId`.

Convert it before returning:

```python
str(result.inserted_id)
```

---

# 23. Interview Questions

1. What is a MongoDB collection?
2. What is a document?
3. Why do we use Pydantic?
4. What is `Field()`?
5. What is `model_dump()` in Pydantic v2?
6. Why separate request and response schemas?
7. What does `insert_one()` return?
8. Why do we convert `inserted_id` to a string?

---

# 24. Assignment

1. Create `SkillCreate`.
2. Create `SkillResponse`.
3. Create `skills.py`.
4. Register the router in `main.py`.
5. Insert three different skills using Swagger.
6. Verify the documents in MongoDB Compass or another MongoDB client.

---

# 25. Mini Challenge

Enhance the `SkillCreate` schema by adding an optional field:

```python
description: str | None = None
```

Then update the API so that the description is also stored when provided.

---

# 26. Best Practices

* Validate all incoming requests.
* Keep schemas separate from API routes.
* Use `model_dump()` with Pydantic v2.
* Organize endpoints using `APIRouter`.
* Return appropriate HTTP status codes (`201 Created` for successful resource creation).

---

# 27. Summary

Today we built our **first real CRUD operation**:

* Designed the `skills` collection.
* Created request and response schemas.
* Implemented a `POST /skills` endpoint.
* Inserted data into MongoDB using Motor.
* Validated requests with Pydantic.
* Returned a proper `201 Created` response.

🎉 **Congratulations!** This is the first time our SkillHub application stores persistent data in MongoDB.

---

# What's Next?

In the next lesson, we'll make this feature more production-ready by:

* Returning a proper **response model** instead of a custom dictionary.
* Creating **GET `/skills`** (Read All).
* Creating **GET `/skills/{id}`** (Read One).
* Understanding MongoDB's `ObjectId`.
* Converting MongoDB documents into clean API responses.
* Introducing reusable helper functions for document serialization.

By the end of the next lesson, we'll have both **Create** and **Read** operations implemented, taking us closer to a complete CRUD API.
