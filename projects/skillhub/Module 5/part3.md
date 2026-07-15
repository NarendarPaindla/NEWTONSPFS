Excellent. Now we will integrate our **security code** into the project.

This lesson changes the User module so that **passwords are securely hashed before being stored**.

> **Important architectural improvement:** We'll create a separate registration schema that includes a password, but we'll keep response schemas free of password fields. This prevents accidentally returning password hashes to clients.

---

# Module 5 — Part 3

# User Registration with Password Hashing

---

# Learning Objectives

By the end of this lesson you'll:

* Accept passwords during registration
* Hash passwords
* Store only hashed passwords
* Never expose passwords in responses
* Prepare the project for Login

---

# Project Changes

We'll update:

```text
app/
│
├── schemas/
│      user_schema.py
│
├── repositories/
│      user_repository.py
│
├── services/
│      user_service.py
│
└── api/
       users.py
```

---

# Step 1 — Update `user_schema.py`

```python
from pydantic import BaseModel
from pydantic import EmailStr
from pydantic import Field


class UserBase(BaseModel):

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


class UserCreate(UserBase):

    password: str = Field(
        ...,
        min_length=8,
        max_length=100
    )


class UserUpdate(UserBase):
    pass


class UserResponse(UserBase):

    id: str
```

---

## Why UserBase?

Instead of repeating

* full_name
* username
* email
* bio
* location

three times,

we define them once.

---

Notice

```python
UserResponse
```

does **NOT** contain

```python
password
```

Very important.

---

# Step 2 — User Repository

No major changes.

Repository simply stores whatever dictionary it receives.

```python
async def create(
    self,
    data: dict
):
    return await database.users.insert_one(
        data
    )
```

Repository should never hash passwords.

That is business logic.

---

# Step 3 — Update `user_service.py`

Import

```python
from app.core.security import (
    hash_password
)
```

---

Update

```python
create_user()
```

Replace

```python
return await user_repository.create(
    user.model_dump()
)
```

with

```python
data = user.model_dump()

data["password"] = hash_password(
    user.password
)

return await user_repository.create(
    data
)
```

---

## What Happens?

User sends

```json
{
    "password":"hello123"
}
```

↓

Hash

↓

Database

```text
$2b$12$Y6...
```

---

# Step 4 — MongoDB Document

Before

```json
{
    "email":"abc@gmail.com",

    "password":"hello123"
}
```

❌

---

After

```json
{
    "email":"abc@gmail.com",

    "password":"$2b$12$AbCdEf..."
}
```

✅

---

# Step 5 — Update Serializer

Current

```python
def user_serializer(user):
```

Keep it exactly like this.

Do **NOT** add

```python
password
```

Example

```python
def user_serializer(user):

    return {

        "id": str(user["_id"]),

        "full_name": user["full_name"],

        "username": user["username"],

        "email": user["email"],

        "bio": user.get("bio"),

        "location": user.get("location")

    }
```

Notice

Password never leaves the backend.

---

# Step 6 — Registration API

Router remains almost unchanged.

```python
@router.post(
    "/",
    status_code=status.HTTP_201_CREATED
)
async def create_user(
    user: UserCreate
):

    result = await user_service.create_user(
        user
    )

    return success_response(
        "User Registered Successfully",
        {
            "id": str(result.inserted_id)
        }
    )
```

---

# Swagger Request

```json
{
    "full_name":"Paindla Narendar Reddy",

    "username":"narendar",

    "email":"narendar@gmail.com",

    "password":"hello123",

    "bio":"Python Trainer",

    "location":"Hyderabad"
}
```

---

# Swagger Response

```json
{
    "success": true,

    "message":"User Registered Successfully",

    "data":{

        "id":"6872..."
    }
}
```

Password

Never returned.

---

# Registration Flow

```text
Client

↓

POST /users

↓

UserCreate Schema

↓

User Service

↓

hash_password()

↓

Repository

↓

MongoDB
```

---

# Verify in MongoDB

Open MongoDB Compass.

You should see

```json
{
    "_id": ObjectId(...),

    "full_name":"Paindla Narendar Reddy",

    "email":"narendar@gmail.com",

    "password":"$2b$12$..."
}
```

Not

```text
hello123
```

---

# Current Authentication Progress

✅ Security Configuration

✅ Password Hashing

✅ Password Storage

⬜ Login

⬜ JWT

⬜ Protected APIs

⬜ Current User

⬜ Refresh Token

⬜ Logout

---

# Best Practices

* Never return password hashes.
* Hash passwords only in the Service Layer.
* Keep repositories free of security logic.
* Validate password length during registration.
* Use dedicated request and response schemas.

---

# Common Mistakes

### Return password

❌ Never.

---

### Store plain text password

❌ Never.

---

### Hash inside Repository

❌ Wrong layer.

---

### Hash inside Router

❌ Wrong layer.

---

Correct

```text
Router

↓

Service

(Hash)

↓

Repository

↓

MongoDB
```

---

# Interview Questions

1. Why shouldn't passwords be stored in plain text?
2. Why is hashing done in the Service Layer?
3. Why doesn't `UserResponse` contain a password?
4. Why is `UserBase` useful?
5. Which layer should know about password hashing?

---

# Summary

Today we completed **secure user registration**.

We now have:

* ✅ Registration schema with password
* ✅ Password validation
* ✅ Password hashing
* ✅ Secure storage
* ✅ Password excluded from responses

SkillHub now stores user credentials securely and is ready for authentication.

---

# 🚀 Next Lesson (Module 5 — Part 4)

We'll implement the **Login API**.

You'll build:

* `LoginRequest` schema
* Find user by email
* Verify password with `verify_password()`
* Generate JWT access token
* Return:

```json
{
    "access_token": "...",
    "token_type": "bearer"
}
```

This is the first point where users will be able to log in and receive a valid JWT token for accessing protected APIs.
