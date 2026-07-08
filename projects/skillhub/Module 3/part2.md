# Backend Development with FastAPI + MongoDB

# **Module 3 — Part 2**

# **Read APIs (GET) + ObjectId + Response Models**

> **Today we're making our first CRUD feature complete.**
>
> Yesterday we learned **Create (POST)**.
>
> Today we'll implement:
>
> * Get All Skills
> * Get Skill by ID
> * Proper Response Models
> * MongoDB ObjectId Handling
> * Serialization

This lesson is extremely important because almost every backend project spends a lot of time converting database objects into API responses.

---

# 1. Learning Objectives

After this lesson you will be able to:

* Understand MongoDB ObjectId
* Read data from MongoDB
* Retrieve one document
* Retrieve multiple documents
* Use `find()`
* Use `find_one()`
* Convert MongoDB documents into JSON
* Create reusable serializer functions
* Use FastAPI Response Models

---

# 2. Before Writing Code

Yesterday we inserted a document.

MongoDB now contains

```json
{
    "_id": ObjectId("6868f72d3..."),
    "name": "Python",
    "category": "Backend",
    "level": "Advanced",
    "experience": 2
}
```

Now the client asks

> "Show me all skills."

Backend should return

```json
[
    {
        "id":"6868...",
        "name":"Python"
    },
    {
        "id":"6869...",
        "name":"FastAPI"
    }
]
```

Seems easy.

But there is one problem.

---

# 3. The ObjectId Problem

MongoDB stores

```python
ObjectId(...)
```

Python understands it.

MongoDB understands it.

JSON DOES NOT.

Example

```python
{
    "_id": ObjectId(...)
}
```

Try returning it directly.

You'll get

```text
ObjectId is not JSON serializable
```

One of the most common beginner errors.

---

# 4. Solution

Convert

```python
ObjectId(...)
```

into

```python
str(ObjectId(...))
```

Example

Instead of

```python
"_id": ObjectId("68682...")
```

Return

```python
"id":"68682..."
```

---

# 5. Never Repeat Yourself (DRY Principle)

Suppose we write

```python
str(document["_id"])
```

inside

* Create API
* Read API
* Update API
* Delete API

Again...

Again...

Again...

Bad Practice.

Professional developers create reusable helper functions.

---

# 6. Create Utils Folder

We already created

```text
utils/
```

Now create

```text
app/utils/serializer.py
```

---

# 7. Create Serializer Function

```python
def skill_serializer(skill) -> dict:
    return {
        "id": str(skill["_id"]),
        "name": skill["name"],
        "category": skill["category"],
        "level": skill["level"],
        "experience": skill["experience"]
    }
```

---

# 8. Why Serializer?

MongoDB Document

↓

```python
{
 "_id":ObjectId(...)
}
```

Serializer

↓

```python
{
"id":"6868..."
}
```

↓

JSON

↓

Frontend

---

# 9. Multiple Documents

One serializer works for one document.

How about 1000 documents?

Create another helper.

```python
def skill_serializer_list(skills):
    return [skill_serializer(skill) for skill in skills]
```

Let's understand.

Suppose

```python
skills
```

contains

```python
[
doc1,
doc2,
doc3
]
```

Python loops through

```python
doc1

↓

serializer

↓

doc2

↓

serializer

↓

doc3

↓

serializer
```

Returns

```python
[
json1,
json2,
json3
]
```

Very reusable.

---

# 10. Update Response Schema

Open

```text
schemas/skill_schema.py
```

Update

```python
from pydantic import BaseModel, Field


class SkillCreate(BaseModel):
    name: str = Field(..., min_length=2)
    category: str
    level: str
    experience: int = Field(..., ge=0)


class SkillResponse(BaseModel):
    id: str
    name: str
    category: str
    level: str
    experience: int
```

Notice

```python
id
```

NOT

```python
_id
```

Frontend should never know MongoDB internals.

---

# 11. GET ALL API

Open

```text
api/skills.py
```

Add

```python
from typing import List

from app.schemas.skill_schema import SkillResponse
from app.utils.serializer import skill_serializer_list
```

Now create API

```python
@router.get(
    "/",
    response_model=List[SkillResponse]
)
async def get_skills():

    skills = await database.skills.find().to_list(length=None)

    return skill_serializer_list(skills)
```

---

# 12. Understanding `find()`

MongoDB

```python
find()
```

means

> Give me ALL documents.

Example

Database

```text
Python

FastAPI

MongoDB
```

Returns

```python
[
Python,
FastAPI,
MongoDB
]
```

---

# 13. What is `to_list()`?

Motor returns a cursor.

Think of Cursor as

```text
Remote Pointer

↓

MongoDB
```

FastAPI cannot return a cursor.

We convert it into a Python list.

```python
.to_list(length=None)
```

returns

```python
[
document,
document,
document
]
```

---

# 14. Why `response_model`?

Many students think

> Response model is optional.

Technically yes.

Professionally no.

FastAPI uses it for

* Validation
* Swagger
* Documentation
* Type Checking
* Automatic Filtering

Always use response models.

---

# 15. Complete Flow

```text
GET /skills

↓

FastAPI

↓

MongoDB

↓

Cursor

↓

List

↓

Serializer

↓

Response Model

↓

JSON

↓

Browser
```

---

# 16. GET BY ID

Client wants

```text
/skills/68683jdj
```

Need

```python
find_one()
```

---

# 17. ObjectId Import

At top

```python
from bson import ObjectId
```

Motor uses BSON internally.

ObjectId belongs to BSON.

---

# 18. Create API

```python
@router.get(
    "/{skill_id}",
    response_model=SkillResponse
)
async def get_skill(skill_id: str):

    skill = await database.skills.find_one(
        {
            "_id": ObjectId(skill_id)
        }
    )

    return skill_serializer(skill)
```

---

# 19. Wait...

Why

```python
ObjectId(skill_id)
```

Client sends

```text
6868373737
```

MongoDB stores

```python
ObjectId(...)
```

We must convert.

Otherwise MongoDB searches

```python
"_id":"68683"
```

instead of

```python
"_id":ObjectId(...)
```

No match found.

This is another common beginner mistake.

---

# 20. Better Error Handling

Suppose ID doesn't exist.

Current response

```python
None
```

Bad API.

Instead

```python
from fastapi import HTTPException
```

Update

```python
if skill is None:
    raise HTTPException(
        status_code=404,
        detail="Skill Not Found"
    )
```

Now API becomes professional.

---

# 21. Complete API

```python
@router.get(
    "/{skill_id}",
    response_model=SkillResponse
)
async def get_skill(skill_id: str):

    skill = await database.skills.find_one(
        {
            "_id": ObjectId(skill_id)
        }
    )

    if skill is None:
        raise HTTPException(
            status_code=404,
            detail="Skill Not Found"
        )

    return skill_serializer(skill)
```

---

# 22. Testing

Swagger

POST

```json
{
"name":"Python",
"category":"Backend",
"level":"Advanced",
"experience":2
}
```

Again

```json
{
"name":"MongoDB",
"category":"Database",
"level":"Intermediate",
"experience":1
}
```

Again

```json
{
"name":"Docker",
"category":"DevOps",
"level":"Beginner",
"experience":0
}
```

---

GET

```text
/skills
```

Returns

```json
[
{
"id":"...",
"name":"Python"
},
{
"id":"...",
"name":"MongoDB"
},
{
"id":"...",
"name":"Docker"
}
]
```

---

GET

```text
/skills/{id}
```

Returns

```json
{
"id":"...",
"name":"Python",
"category":"Backend",
"level":"Advanced",
"experience":2
}
```

---

# 23. Folder Structure

```text
app/

api/
    skills.py

schemas/
    skill_schema.py

utils/
    serializer.py
```

Notice how each file has one responsibility:

* `schemas/` → Validation
* `api/` → HTTP endpoints
* `utils/` → Serialization

This separation keeps the project maintainable.

---

# 24. Common Errors

## Invalid ObjectId

```
InvalidId
```

Reason

User entered

```
abc
```

Instead of

```
68682...
```

We'll learn to handle this gracefully in a later lesson.

---

## ObjectId Not Serializable

Reason

Forgot serializer.

---

## Cursor Not JSON Serializable

Reason

Forgot

```python
.to_list()
```

---

## 404 Error

Reason

Document not found.

Correct behavior.

---

# 25. Interview Questions

1. What is ObjectId?
2. Why can't JSON serialize ObjectId?
3. What is BSON?
4. Difference between find() and find_one()?
5. What is a Cursor?
6. Why use Serializer Functions?
7. Why use Response Models?

---

# 26. Assignment

Create

* GET All Skills
* GET Skill by ID

Insert

10 skills

Retrieve

* All skills
* Individual skill

Test using Swagger.

---

# 27. Mini Challenge

Sort the skills alphabetically by name before returning them.

(Hint: Explore MongoDB cursor sorting. We'll formally cover sorting in the next lesson.)

---

# 28. Best Practices

* Never expose MongoDB's `_id` directly in API responses.
* Always convert `ObjectId` values to strings before returning them.
* Use reusable serializer functions instead of repeating conversion logic.
* Define `response_model` for every endpoint.
* Return `404 Not Found` when a requested resource doesn't exist instead of returning `None`.

---

# 29. Summary

Today we completed the **Read** portion of CRUD by implementing:

* `GET /skills` to retrieve all skills.
* `GET /skills/{skill_id}` to retrieve a single skill.
* Reusable serializer functions.
* Proper response models.
* `ObjectId` conversion.
* Basic `404 Not Found` handling.

Our SkillHub API can now **Create** and **Read** data from MongoDB.

---

# Next Lesson

We'll continue with **Module 3 — Part 3**, where we'll implement the remaining CRUD operations:

* `PUT /skills/{skill_id}` (Update)
* `DELETE /skills/{skill_id}` (Delete)
* Proper update validation
* MongoDB's `update_one()` and `delete_one()`
* `matched_count` vs `modified_count`
* `deleted_count`
* Improved error handling for update and delete operations

By the end of the next lesson, we'll have a complete **CRUD API** for the `skills` collection.
