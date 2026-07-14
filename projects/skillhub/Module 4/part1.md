# Backend Development with FastAPI + MongoDB

# **Module 4 — Part 1**

# **Introducing the Users Collection & MongoDB Relationships**

> ⭐ **Today, our project starts becoming a real application.**
>
> Until now, anyone could create skills, and there was no concept of ownership.
>
> From this lesson onward, we'll introduce **Users** so that every skill belongs to a specific user.

---

# 1. Learning Objectives

By the end of this lesson, you will understand:

* Why we need a `users` collection
* MongoDB relationship design
* One-to-Many relationships
* Referencing vs Embedding
* Designing a User document
* Creating User schemas
* Creating the User repository, service, and API

---

# 2. Current Problem

Our current `skills` collection looks like this:

```json
{
    "_id": ObjectId("68682..."),
    "name": "Python",
    "category": "Backend",
    "level": "Advanced",
    "experience": 2
}
```

Question:

**Who owns this skill?**

We don't know.

Maybe:

* Narendar
* Rahul
* Priya

The database has no way to identify the owner.

---

# 3. Real-World Example

Think about GitHub.

Repository:

```text
FastAPI-Course
```

Question:

Who owns it?

```text
Narendar
```

Similarly,

Skill:

```text
Python
```

Question:

Who owns it?

```text
User
```

So every Skill must belong to one User.

---

# 4. Relationship

One User

↓

Many Skills

```text
User

↓

Python

↓

MongoDB

↓

Docker

↓

Redis

↓

FastAPI
```

This is called

# **One-to-Many Relationship**

---

# 5. MongoDB Relationship Options

MongoDB supports two approaches.

## Option 1 — Embedding

```json
{
    "name":"Narendar",

    "skills":[
        {
            "name":"Python"
        },
        {
            "name":"FastAPI"
        }
    ]
}
```

Everything inside one document.

---

## Option 2 — Referencing

Users Collection

```json
{
    "_id":"U1",

    "name":"Narendar"
}
```

Skills Collection

```json
{
    "name":"Python",

    "user_id":"U1"
}
```

Skill stores only the User ID.

---

# 6. Which One Should We Choose?

Embedding works well when:

* Data is small.
* Rarely changes.
* Always read together.

Example:

* Address
* Preferences
* Settings

---

Referencing works well when:

* Large data
* Frequently updated
* Independent collections

Examples:

* Users
* Orders
* Posts
* Comments
* Skills

---

# 7. Our Choice

We'll use

# **Referencing**

Because

One user may eventually have:

* 100 Skills
* 200 Projects
* 500 Comments

Embedding all of that inside one document is not practical.

---

# 8. Database Design

```text
skillhub_db

│

├── users

└── skills
```

Users

```json
{
"_id":"U1",

"name":"Narendar"
}
```

Skills

```json
{
"name":"Python",

"user_id":"U1"
}
```

Now every skill has an owner.

---

# 9. Designing User Document

Version 1

```json
{
    "_id": ObjectId(),

    "full_name":"Narendar Reddy",

    "email":"narendar@gmail.com",

    "username":"narendar",

    "bio":"Python Backend Developer",

    "location":"Hyderabad",

    "created_at":"2026-07-14T10:00:00Z"
}
```

Notice

No password yet.

Authentication comes in Module 5.

For now, we focus on the User profile.

---

# 10. Folder Structure

```text
app/

api/
    users.py

schemas/
    user_schema.py

repositories/
    user_repository.py

services/
    user_service.py
```

Every collection gets its own layers.

---

# 11. User Schema

**File**

```text
schemas/user_schema.py
```

```python
from pydantic import BaseModel, EmailStr, Field


class UserCreate(BaseModel):

    full_name: str = Field(
        ...,
        min_length=2,
        max_length=100
    )

    username: str = Field(
        ...,
        min_length=3,
        max_length=30
    )

    email: EmailStr

    bio: str | None = None

    location: str | None = None
```

---

# 12. Why `EmailStr`?

Instead of

```python
email:str
```

Use

```python
EmailStr
```

Example

Valid

```text
abc@gmail.com
```

Invalid

```text
abc

gmail

abc@
```

Pydantic automatically validates email format.

---

# 13. Response Schema

```python
class UserResponse(BaseModel):

    id: str

    full_name: str

    username: str

    email: str

    bio: str | None

    location: str | None
```

Notice

Still no password.

Passwords are never returned in API responses.

---

# 14. Repository

```python
from app.db.database import database


class UserRepository:

    async def create(
        self,
        user
    ):
        return await database.users.insert_one(user)

    async def get_by_email(
        self,
        email
    ):
        return await database.users.find_one(
            {
                "email": email
            }
        )

    async def get_all(self):
        return await (
            database.users
            .find()
            .to_list(length=None)
        )


user_repository = UserRepository()
```

---

# 15. Why `get_by_email()`?

Soon we'll implement authentication.

Login happens using

```text
Email

+

Password
```

So we need a way to find users by email.

We're preparing for future modules.

---

# 16. Service Layer

```python
from app.repositories.user_repository import (
    user_repository
)


class UserService:

    async def create_user(
        self,
        user
    ):
        return await user_repository.create(
            user.model_dump()
        )

    async def get_users(self):
        return await user_repository.get_all()


user_service = UserService()
```

Simple for now.

Business logic will grow later.

---

# 17. User API

```python
from fastapi import APIRouter

from app.services.user_service import (
    user_service
)

router = APIRouter(

    prefix="/users",

    tags=["Users"]
)
```

---

POST

```python
@router.post("/")
async def create_user(user: UserCreate):

    result = await user_service.create_user(
        user
    )

    return success_response(

        "User Created",

        {

            "id": str(result.inserted_id)

        }
    )
```

---

GET

```python
@router.get("/")
async def get_users():

    users = await user_service.get_users()

    return users
```

We'll improve the GET response with serializers and response models in the next lesson.

---

# 18. Register Router

In `main.py`:

```python
from app.api.users import router as user_router

app.include_router(user_router)
```

Now Swagger will show:

```text
Users

Skills
```

Our API is growing.

---

# 19. Current Database

```text
skillhub_db

│

├── users

│     Narendar

│     Rahul

│     Priya

│

└── skills

      Python

      FastAPI

      Docker
```

The next step is to connect them.

---

# 20. Common Errors

### Invalid Email

```text
value is not a valid email address
```

Use:

```text
abc@gmail.com
```

---

### Duplicate Email

Currently,

MongoDB allows

```text
abc@gmail.com

abc@gmail.com
```

We'll prevent duplicates in the next lesson using **unique indexes** and service validation.

---

### Missing Required Field

Example

```json
{
"username":"narendar"
}
```

Fails because

```text
full_name

email
```

are required.

---

# 21. Interview Questions

1. What is a One-to-Many relationship?
2. Difference between Embedding and Referencing?
3. Why use Referencing in SkillHub?
4. Why use `EmailStr`?
5. Why shouldn't passwords be returned in API responses?
6. What responsibilities belong to the User Repository?

---

# 22. Assignment

Create:

* User schemas
* User repository
* User service
* User API

Insert at least **5 users**.

Retrieve all users.

Verify the `users` collection in MongoDB.

---

# 23. Mini Challenge

Add an optional field:

```python
profile_image: str | None = None
```

Example:

```text
https://example.com/images/user1.png
```

Include it in both the request and response schemas.

---

# 24. Best Practices

* Keep Users and Skills in separate collections.
* Use references (`user_id`) for relationships between collections.
* Validate emails using `EmailStr`.
* Never expose sensitive information in responses.
* Create repositories and services for every collection to keep the architecture consistent.

---

# 25. Summary

Today we introduced the **Users** collection and learned how MongoDB relationships work.

We covered:

* ✅ One-to-Many relationships
* ✅ Referencing vs Embedding
* ✅ User document design
* ✅ User schemas
* ✅ User repository
* ✅ User service
* ✅ User API

Our project now has two independent collections:

* `users`
* `skills`

---

# 🚀 Next Lesson

In the next lesson, we'll **connect these two collections**.

We'll modify the Skills module so that:

* Every skill belongs to a specific user (`user_id`)
* A skill cannot be created for a non-existent user
* We'll validate relationships before inserting data
* We'll learn how MongoDB handles references

By the end of that lesson, our data model will resemble a real-world backend, laying the groundwork for JWT authentication, where the `user_id` will come directly from the logged-in user instead of being supplied manually.
