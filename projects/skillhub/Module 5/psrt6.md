Excellent. This is the biggest improvement we'll make to the project so far.

Up to this point, the client could send:

```json
{
    "name": "Python",
    "user_id": "6872abcd..."
}
```

This is **not secure** because anyone could change the `user_id` and create skills for another user.

From now on, **the backend decides the owner**.

---

# Module 5 — Part 6

# Secure Skill APIs Using Current User

---

# Learning Objectives

By the end of this lesson you'll learn:

* Remove `user_id` from requests
* Automatically assign ownership
* Protect Skill APIs
* Ownership-based design
* Why trusting the client is dangerous

---

# Before Authentication

```text
Client

↓

POST /skills

↓

{
"user_id":"6872..."
}

↓

Database
```

Client chooses owner.

❌

---

# After Authentication

```text
Client

↓

JWT

↓

Backend

↓

Current User

↓

Database
```

Backend chooses owner.

✅

---

# Project Files That Change

```text
app/

├── schemas/
│      skill_schema.py
│
├── services/
│      skill_service.py
│
└── api/
       skills.py
```

---

# Step 1 — Update `skill_schema.py`

## OLD

```python
class SkillCreate(SkillBase):
    user_id: str


class SkillUpdate(SkillBase):
    user_id: str
```

---

## NEW

```python
from pydantic import BaseModel
from pydantic import Field


class SkillBase(BaseModel):

    name: str = Field(
        ...,
        min_length=2,
        max_length=100
    )

    category: str = Field(
        ...,
        min_length=2,
        max_length=50
    )

    level: str = Field(
        ...,
        min_length=2,
        max_length=30
    )

    experience: int = Field(
        ...,
        ge=0,
        le=50
    )


class SkillCreate(SkillBase):
    pass


class SkillUpdate(SkillBase):
    pass


class SkillResponse(SkillBase):

    id: str

    user_id: str
```

Notice

No

```text
user_id
```

Client cannot send it anymore.

---

# Swagger Request

Now

```json
{
    "name":"Python",

    "category":"Backend",

    "level":"Advanced",

    "experience":5
}
```

Much better.

---

# Step 2 — Update Service

Old

```python
async def create_skill(
    self,
    skill
)
```

New

```python
async def create_skill(
    self,
    skill,
    current_user
)
```

---

Inside

Replace

```python
object_id = validate_object_id(
    skill.user_id
)
```

with

```python
object_id = current_user["_id"]
```

No validation needed.

JWT already validated.

---

Remove

```python
user = await user_repository.get_by_id(...)
```

No need.

Already authenticated.

---

Build data

```python
data = skill.model_dump()

data["user_id"] = object_id
```

Done.

---

# New Create Flow

```python
async def create_skill(
    self,
    skill,
    current_user
):

    existing_skill = await (
        skill_repository.get_by_name(
            skill.name
        )
    )

    if existing_skill:

        raise HTTPException(

            status_code=400,

            detail="Skill Already Exists"

        )

    data = skill.model_dump()

    data["user_id"] = current_user["_id"]

    return await skill_repository.create(
        data
    )
```

---

# Step 3 — Protect API

Imports

```python
from fastapi import Depends

from app.dependencies.auth import (
    get_current_user
)
```

---

Update

```python
@router.post("/")
```

to

```python
@router.post("/")
async def create_skill(

    skill: SkillCreate,

    current_user=Depends(

        get_current_user

    )

):
```

Call

```python
result = await skill_service.create_skill(

    skill,

    current_user

)
```

Done.

---

# New Flow

```text
POST /skills

↓

JWT

↓

Current User

↓

Skill Service

↓

MongoDB
```

---

# Step 4 — Protect Get My Skills

Instead of

```http
GET /skills/user/{id}
```

We'll add

```http
GET /skills/me
```

Much cleaner.

---

Router

```python
@router.get("/me")
async def my_skills(

    current_user=Depends(

        get_current_user

    )

):

    skills = await skill_service.get_user_skills(

        str(
            current_user["_id"]
        )

    )

    return success_response(

        "My Skills",

        skill_serializer_list(
            skills
        )

    )
```

Notice

Client never sends

```text
user_id
```

Again.

---

# Step 5 — Protect Update

Old

```python
PUT

/skills/{id}
```

Allowed

Anyone.

---

New

```python
current_user=Depends(
get_current_user
)
```

We'll also check ownership in the next lesson.

---

# Step 6 — Protect Delete

Same

```python
Depends(
get_current_user
)
```

We'll add ownership verification next.

---

# Swagger

Now

Click

Authorize

↓

Paste Token

↓

Every Skill API

Works.

Without token

↓

401.

---

# Request Flow

```text
Client

↓

Bearer Token

↓

OAuth2

↓

JWT Decode

↓

Current User

↓

Skill Service

↓

MongoDB
```

---

# Why Remove user_id?

Suppose

Bad user

Changes

```json
{
"user_id":"admin_id"
}
```

Old System

↓

Admin Skill Created

😱

New System

↓

Ignored

Backend

Uses JWT.

Secure.

---

# Production Architecture

```text
Client

↓

JWT

↓

Current User

↓

Business Logic

↓

Repository

↓

MongoDB
```

Never

```text
Client

↓

user_id

↓

Database
```

---

# What Still Needs Improvement?

Current duplicate check

```python
get_by_name()
```

Problem

User A

Python

↓

User B

Python

Rejected.

Wrong.

---

Next

We'll use

```text
user_id

+

skill_name
```

Now

User A

Python

✔

User B

Python

✔

---

# Authentication Progress

✅ Registration

✅ Hash Password

✅ Login

✅ JWT

✅ OAuth2

✅ Current User

✅ Protected Skill Creation

⬜ Ownership Validation

⬜ Refresh Token

⬜ Logout

⬜ Roles

---

# Best Practices

* Never trust IDs from the client for ownership.
* Use the authenticated user from the JWT.
* Keep authentication in dependencies, not in routers.
* Expose `/me` endpoints for user-owned resources instead of requiring user IDs in the URL.

---

# Interview Questions

1. Why remove `user_id` from `SkillCreate`?
2. Why is using JWT safer than accepting `user_id` from the client?
3. What is the purpose of `/skills/me`?
4. Why should ownership come from the token?
5. Which layer should assign the `user_id` to a new skill?

---

# Summary

Today we transformed the Skill module into a secure, user-aware API.

We achieved:

* ✅ Removed `user_id` from request models
* ✅ Assigned ownership from the authenticated user
* ✅ Protected skill creation
* ✅ Added `/skills/me`
* ✅ Prepared the project for ownership validation

---

# 🚀 Next Lesson (Module 5 — Part 7)

We'll implement **Resource Ownership Authorization**.

This is where you'll learn how to ensure:

* User A **cannot** update User B's skills.
* User A **cannot** delete User B's skills.
* Every update/delete verifies ownership before making changes.

This is one of the most important security patterns used in real-world backend applications.
