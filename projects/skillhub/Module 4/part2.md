# Backend Development with FastAPI + MongoDB

# **Module 4 — Part 2**

# **MongoDB Relationships (One-to-Many)**

## **Connecting Users and Skills**

> ⭐ **This is where our database starts behaving like a real-world application.**
>
> Until now:
>
> * Users are stored.
> * Skills are stored.
>
> But they have **no relationship**.
>
> Today, we'll connect them.

---

# 1. Learning Objectives

After this lesson, you will be able to:

* Create relationships in MongoDB
* Store references using ObjectId
* Validate parent-child relationships
* Understand foreign keys in MongoDB
* Modify existing collections
* Design scalable data models

---

# 2. Current Database

Currently our database looks like this:

## Users Collection

```json
{
    "_id": ObjectId("U1"),
    "full_name": "Narendar Reddy",
    "email": "narendar@gmail.com"
}
```

Another user

```json
{
    "_id": ObjectId("U2"),
    "full_name": "Rahul",
    "email": "rahul@gmail.com"
}
```

---

## Skills Collection

```json
{
    "_id": ObjectId("S1"),
    "name": "Python",
    "category": "Backend"
}
```

Question:

Who owns Python?

Nobody knows.

This is a bad database design.

---

# 3. Goal

We want this:

```text
Users
──────────────

Narendar
      │
      │
      ▼
Python
FastAPI
MongoDB

Rahul
     │
     ▼
Java
Spring Boot
Docker
```

One User

↓

Many Skills

---

# 4. Database Design

Instead of

```json
{
    "name":"Python"
}
```

We'll store

```json
{
    "name":"Python",

    "user_id":ObjectId("U1")
}
```

Now MongoDB knows

Python belongs to User U1.

---

# 5. Visual Architecture

```text
Users Collection

+----------------------+
| _id = U1             |
| Narendar             |
+----------------------+

          ▲
          │
          │ user_id
          │

+----------------------+
| Python               |
+----------------------+

+----------------------+
| FastAPI              |
+----------------------+

+----------------------+
| Docker               |
+----------------------+
```

This is called

**Reference Relationship**

---

# 6. Update Skill Document

Old

```json
{
    "_id": ObjectId(),

    "name":"Python",

    "category":"Backend",

    "level":"Advanced"
}
```

New

```json
{
    "_id": ObjectId(),

    "name":"Python",

    "category":"Backend",

    "level":"Advanced",

    "experience":2,

    "user_id":ObjectId("6868abc")
}
```

Only one new field.

But this field changes everything.

---

# 7. Update Request Schema

Open

```text
schemas/

skill_schema.py
```

Update

```python
from pydantic import BaseModel, Field


class SkillCreate(BaseModel):

    name: str = Field(..., min_length=2)

    category: str

    level: str

    experience: int = Field(..., ge=0)

    user_id: str
```

---

# Why String?

Frontend sends

```json
{
"user_id":"68683abc..."
}
```

NOT

```python
ObjectId(...)
```

Backend converts it.

---

# 8. Update Response Schema

```python
class SkillResponse(BaseModel):

    id: str

    name: str

    category: str

    level: str

    experience: int

    user_id: str
```

---

# 9. Serializer Update

Open

```text
utils/

serializer.py
```

Update

```python
def skill_serializer(skill):

    return {

        "id":str(skill["_id"]),

        "name":skill["name"],

        "category":skill["category"],

        "level":skill["level"],

        "experience":skill["experience"],

        "user_id":str(skill["user_id"])
    }
```

Notice

Both IDs become strings.

---

# 10. Validate User Exists

Question

What if client sends

```text
user_id = 12345
```

and that user doesn't exist?

Should backend still create the skill?

No.

That would create invalid data.

---

# 11. Service Layer

This is **business logic**.

Perfect place.

Open

```text
services/

skill_service.py
```

---

Import

```python
from bson import ObjectId

from fastapi import HTTPException

from app.repositories.user_repository import (
    user_repository
)
```

---

# 12. Validate User

Before saving

```python
user = await user_repository.get_by_id(
    ObjectId(skill.user_id)
)
```

Wait...

Repository doesn't have

```python
get_by_id()
```

Let's create it.

---

# 13. Update User Repository

```python
async def get_by_id(
    self,
    object_id
):

    return await database.users.find_one(

        {

            "_id":object_id

        }
    )
```

Done.

---

# 14. Continue Service

```python
if user is None:

    raise HTTPException(

        status_code=404,

        detail="User Not Found"
    )
```

Excellent.

Now

Only existing users can own skills.

---

# 15. Save Skill

Convert

```python
data = skill.model_dump()
```

Now

Convert

```python
data["user_id"] = ObjectId(
    skill.user_id
)
```

Why?

Because MongoDB stores

```python
ObjectId(...)
```

NOT

```python
"68683..."
```

---

Now save

```python
await repository.create(data)
```

Done.

---

# 16. Complete Flow

```text
Client

↓

POST /skills

↓

Router

↓

Service

↓

Check User Exists

↓

Yes

↓

Convert user_id

↓

Repository

↓

MongoDB

↓

Skill Saved
```

Beautiful.

---

# 17. Invalid Flow

```text
Client

↓

POST /skills

↓

User Doesn't Exist

↓

404

↓

Nothing Saved
```

This protects database integrity.

---

# 18. Testing

Create User

```http
POST /users
```

↓

Returns

```text
68682abc
```

Now

Create Skill

```json
{
"name":"Python",

"category":"Backend",

"level":"Advanced",

"experience":2,

"user_id":"68682abc"
}
```

Success.

---

Try

```json
"user_id":"123456"
```

Response

```json
{
"success":false,

"message":"User Not Found"
}
```

Excellent.

---

# 19. Database

Users

```json
{
"_id":ObjectId("U1"),

"full_name":"Narendar"
}
```

Skills

```json
{
"name":"Python",

"user_id":ObjectId("U1")
}
```

Relationship established.

---

# 20. Fetch User Skills

Question

How do we get

Narendar's Skills?

Very simple.

MongoDB

```python
.find(

{

"user_id":ObjectId(user_id)

}
)
```

Returns

```text
Python

FastAPI

Docker
```

We'll build this API today.

---

# 21. Repository

Add

```python
async def get_by_user(
    self,
    object_id
):

    return await (

        database.skills

        .find(

            {

                "user_id":object_id

            }

        )

        .to_list(length=None)

    )
```

---

# 22. Service

```python
async def get_user_skills(
    self,
    user_id
):

    return await repository.get_by_user(

        ObjectId(user_id)

    )
```

---

# 23. API

```python
@router.get(

"/user/{user_id}"

)
async def get_user_skills(

    user_id:str

):

    skills = await skill_service.get_user_skills(

        user_id

    )

    return skill_serializer_list(

        skills

    )
```

Swagger

```
GET

/skills/user/{user_id}
```

Amazing.

---

# 24. API Testing

Create

Narendar

↓

Python

↓

FastAPI

↓

MongoDB

Create

Rahul

↓

Java

↓

Spring

↓

Docker

Now

```http
GET /skills/user/U1
```

Returns

```text
Python

FastAPI

MongoDB
```

Perfect.

---

# 25. Current Architecture

```text
Users

       ▲

       │

       │

Skills

       ▲

       │

Service Validation

       ▲

       │

Repository

       ▲

       │

MongoDB
```

Our database is becoming relational while still using MongoDB.

---

# 26. Common Errors

### Invalid ObjectId

```
abc123
```

Use our `validate_object_id()` utility before converting to `ObjectId`.

---

### Forget Conversion

Wrong

```python
"user_id":"6868"
```

Correct

```python
"user_id":ObjectId(...)
```

---

### Skip User Validation

Then

Skills may reference users that don't exist.

Bad database.

---

# 27. Interview Questions

1. What is a One-to-Many relationship?
2. Why store `user_id` inside `skills`?
3. Why validate parent records before inserting child records?
4. Difference between Embedding and Referencing?
5. Why convert string IDs into `ObjectId`?
6. How do you fetch all skills belonging to one user?

---

# 28. Assignment

Implement:

* `user_id` in the Skill schema.
* User validation in the Service Layer.
* `GET /skills/user/{user_id}` endpoint.
* Insert two users and at least three skills for each.
* Verify that each user only receives their own skills.

---

# 29. Mini Challenge

Add a new endpoint:

```http
GET /users/{user_id}/skills/count
```

Expected response:

```json
{
    "user_id": "68682abc...",
    "total_skills": 6
}
```

**Hint:** Add a repository method using:

```python
count_documents(
    {
        "user_id": object_id
    }
)
```

---

# 30. Best Practices

* Always validate referenced documents before saving child records.
* Store references as `ObjectId` in MongoDB, not strings.
* Keep relationship validation in the Service Layer.
* Never trust IDs sent by clients without validation.
* Design collections to support future authentication.

---

# 31. Summary

Today we transformed SkillHub from independent collections into a **connected data model**.

We implemented:

* ✅ One-to-Many relationship (`User → Skills`)
* ✅ `user_id` reference
* ✅ Parent validation
* ✅ Fetch skills by user
* ✅ Relationship-aware service logic

Our database now closely resembles what you'll see in real production applications.

---

# 🚀 Next Lesson (Very Important)

Our current relationship has one limitation:

```http
GET /skills/user/{user_id}
```

The frontend gets only the skills.

But often the frontend wants:

```json
{
    "user": {
        "full_name": "Narendar Reddy",
        "email": "narendar@gmail.com"
    },
    "skills": [
        {
            "name": "Python"
        },
        {
            "name": "FastAPI"
        }
    ]
}
```

To build this efficiently, we'll learn **MongoDB Aggregation Framework**—specifically:

* `$lookup`
* `$match`
* `$project`

These allow MongoDB to combine data from multiple collections, similar to SQL joins, while staying within MongoDB's document model. This is one of the most valuable MongoDB skills for backend developers.
