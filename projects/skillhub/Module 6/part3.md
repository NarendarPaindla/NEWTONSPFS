Perfect. From now on, I'll follow your rule:

* ✅ One lesson = One complete file
* ✅ No partial snippets
* ✅ Complete replaceable file
* ✅ Based on your existing project

---

# Module 6 — Part 3

## Complete Updated File

**File**

```text
app/schemas/user_schema.py
```

Replace your existing file completely with the following.

```python
from pydantic import BaseModel
from pydantic import EmailStr
from pydantic import Field

from app.enums.roles import UserRole


class UserBase(BaseModel):
    full_name: str = Field(
        ...,
        min_length=2,
        max_length=100,
        description="Full Name"
    )

    username: str = Field(
        ...,
        min_length=3,
        max_length=30,
        description="Unique Username"
    )

    email: EmailStr

    bio: str | None = Field(
        default=None,
        max_length=500
    )

    location: str | None = Field(
        default=None,
        max_length=100
    )


class UserCreate(UserBase):
    password: str = Field(
        ...,
        min_length=8,
        max_length=100,
        description="User Password"
    )


class UserUpdate(UserBase):
    pass


class UserResponse(UserBase):
    id: str

    role: UserRole


class UserLogin(BaseModel):
    email: EmailStr

    password: str


class ChangePassword(BaseModel):
    old_password: str = Field(
        ...,
        min_length=8
    )

    new_password: str = Field(
        ...,
        min_length=8
    )


class UserRoleUpdate(BaseModel):
    role: UserRole
```

---

# What Changed?

Compared to your previous schema:

### ✅ Added

```python
from app.enums.roles import UserRole
```

---

### UserResponse

Now returns

```python
role: UserRole
```

Response

```json
{
    "id": "6872....",
    "full_name": "Narendar",
    "username": "narendar",
    "email": "narendar@gmail.com",
    "bio": "Backend Trainer",
    "location": "Hyderabad",
    "role": "USER"
}
```

---

### Added

```python
UserLogin
```

Later we'll reuse it for authentication.

---

### Added

```python
ChangePassword
```

Later

```
PUT /users/change-password
```

---

### Added

```python
UserRoleUpdate
```

Later

```
PATCH /users/{id}/role
```

Admin only.

---

# Why Don't We Add Role to UserCreate?

Many beginners do

```python
class UserCreate(BaseModel):
    ...
    role: UserRole
```

That is a huge security problem.

Imagine someone sends

```json
{
    "full_name":"ABC",
    "email":"abc@gmail.com",
    "password":"12345678",
    "role":"ADMIN"
}
```

Now anyone can become Admin.

❌ Wrong.

Instead,

the backend will automatically assign

```python
UserRole.USER
```

inside the service layer.

Only an existing Admin can later change another user's role.

---

# User Registration Flow

```
Client

↓

POST /users

↓

UserCreate

↓

UserService

↓

role = USER

↓

MongoDB
```

---

# MongoDB User Document

Now every user document will look like

```json
{
    "_id": ObjectId("6872..."),

    "full_name": "Narendar",

    "username": "narendar",

    "email": "narendar@gmail.com",

    "password": "$2b$12$...",

    "bio": "Backend Trainer",

    "location": "Hyderabad",

    "role": "USER"
}
```

---

# Best Practices

✔ Never allow role in registration.

✔ Always return role in API responses.

✔ Use Enum instead of strings.

✔ Keep role changes separate from profile updates.

---

# Interview Questions

1. Why shouldn't users choose their own role?
2. Why use Enum for roles?
3. Why is `UserRoleUpdate` a separate schema?
4. What is the default role for new users?
5. Why return the role in `UserResponse`?

---

# Summary

Today we updated the complete `user_schema.py`.

New additions:

* ✅ UserRole support
* ✅ UserResponse with role
* ✅ UserLogin schema
* ✅ ChangePassword schema
* ✅ UserRoleUpdate schema

---

# 🚀 Next Lesson (One Complete File)

The next lesson will provide the **complete updated**:

```text
app/services/user_service.py
```

We'll update it so that:

* Every new user automatically gets `UserRole.USER`
* Password hashing remains intact
* Duplicate checks remain intact
* Future role management is supported

This will be a **complete replaceable file**, not snippets.
