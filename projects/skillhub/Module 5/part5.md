Excellent. This is one of the **most important FastAPI lessons**.

After this lesson, you will understand **how protected APIs actually work**.

From now on, users will no longer send:

```json
{
    "user_id": "6872abc..."
}
```

Instead, they will simply send

```http
Authorization: Bearer eyJhbGc...
```

and the backend will automatically identify the logged-in user.

---

# Module 5 — Part 5

# OAuth2PasswordBearer + JWT Verification + Current User

---

# Learning Objectives

By the end of this lesson, you'll learn:

* OAuth2PasswordBearer
* Depends()
* JWT Decode
* JWT Verification
* Current User
* Protected APIs
* Authorization Header
* Dependency Injection

---

# Authentication Flow

```text
Client

↓

POST /auth/login

↓

JWT Token

↓

Client Stores Token

↓

Authorization Header

↓

OAuth2PasswordBearer

↓

Decode JWT

↓

Current User

↓

Protected API
```

---

# What is OAuth2PasswordBearer?

FastAPI provides

```python
OAuth2PasswordBearer
```

It automatically reads this header:

```http
Authorization: Bearer eyJhbGc...
```

You do NOT manually parse headers.

FastAPI does it for you.

---

# Visual

```text
Authorization Header

↓

OAuth2PasswordBearer

↓

Extract Token

↓

Return String
```

---

# Updated Project Structure

```text
app/

├── api/
│     auth.py
│     users.py
│     skills.py
│
├── core/
│     config.py
│     security.py
│
├── dependencies/
│     auth.py        ⭐ NEW
│
└── services/
```

We create a separate dependency because many APIs will reuse it.

---

# Step 1 — Create `app/dependencies/auth.py`

Imports

```python
from fastapi import Depends
from fastapi import HTTPException
from fastapi import status

from fastapi.security import (
    OAuth2PasswordBearer
)

from jose import JWTError
from jose import jwt

from bson import ObjectId

from app.core.config import settings

from app.db.database import database
```

---

# Step 2 — OAuth2 Scheme

```python
oauth2_scheme = OAuth2PasswordBearer(

    tokenUrl="/api/v1/auth/login"

)
```

What happens?

FastAPI now knows

Where users obtain tokens.

Swagger also shows the **Authorize** button automatically.

---

# Visual

```text
Swagger

↓

Authorize

↓

Paste JWT

↓

Every Request Sends

Authorization Header
```

---

# Step 3 — Create Dependency

```python
async def get_current_user(

    token: str = Depends(
        oauth2_scheme
    )

):
```

Notice

```python
Depends()
```

FastAPI injects

the token

automatically.

You never call

```python
get_current_user()
```

yourself.

---

# Step 4 — Decode JWT

```python
try:

    payload = jwt.decode(

        token,

        settings.secret_key,

        algorithms=[
            settings.algorithm
        ]

    )
```

If JWT invalid

↓

Exception.

---

# Step 5 — Extract User ID

```python
    user_id = payload.get(
        "sub"
    )
```

Remember

During Login

we stored

```python
{
    "sub": user_id
}
```

Now

we read it.

---

# Validate

```python
    if user_id is None:

        raise HTTPException(

            status_code=401,

            detail="Invalid Token"

        )
```

---

# Step 6 — Find User

```python
    user = await database.users.find_one(

        {

            "_id": ObjectId(
                user_id
            )

        }

    )
```

---

If not found

```python
    if user is None:

        raise HTTPException(

            status_code=401,

            detail="User Not Found"

        )
```

---

# Step 7 — Return User

```python
    return user
```

---

# JWT Error

```python
except JWTError:

    raise HTTPException(

        status_code=401,

        detail="Invalid Token"

    )
```

---

# Complete Dependency

```python
from bson import ObjectId

from fastapi import Depends
from fastapi import HTTPException

from fastapi.security import (
    OAuth2PasswordBearer
)

from jose import JWTError
from jose import jwt

from app.core.config import settings
from app.db.database import database


oauth2_scheme = OAuth2PasswordBearer(

    tokenUrl="/api/v1/auth/login"

)


async def get_current_user(

    token: str = Depends(
        oauth2_scheme
    )

):

    try:

        payload = jwt.decode(

            token,

            settings.secret_key,

            algorithms=[
                settings.algorithm
            ]

        )

        user_id = payload.get(
            "sub"
        )

        if user_id is None:

            raise HTTPException(

                status_code=401,

                detail="Invalid Token"

            )

        user = await database.users.find_one(

            {

                "_id": ObjectId(
                    user_id
                )

            }

        )

        if user is None:

            raise HTTPException(

                status_code=401,

                detail="User Not Found"

            )

        return user

    except JWTError:

        raise HTTPException(

            status_code=401,

            detail="Invalid Token"

        )
```

---

# Step 8 — First Protected API

Inside

```python
users.py
```

Create

```python
from fastapi import Depends

from app.dependencies.auth import (
    get_current_user
)
```

---

API

```python
@router.get("/me")
async def me(

    current_user = Depends(

        get_current_user

    )

):

    return success_response(

        "Current User",

        user_serializer(

            current_user

        )

    )
```

---

# What Happens?

Client

Sends

```http
GET /api/v1/users/me
```

Header

```http
Authorization:

Bearer eyJhbGc...
```

FastAPI

↓

OAuth2

↓

Decode JWT

↓

Find User

↓

Inject

```python
current_user
```

Automatically.

---

# Response

```json
{
    "success":true,

    "message":"Current User",

    "data":{

        "id":"6872...",

        "email":"narendar@gmail.com"

    }
}
```

---

# Swagger

Click

```text
Authorize
```

Paste

```text
Bearer eyJhbGc...
```

Now

All protected APIs

work.

---

# Dependency Injection

This

```python
Depends(
get_current_user
)
```

means

Before

running API

Execute

```python
get_current_user()
```

If success

↓

Continue.

Else

↓

401 Unauthorized.

---

Visual

```text
Request

↓

Depends()

↓

JWT Decode

↓

Current User

↓

API
```

---

# Current Authentication Flow

```text
Register

↓

Hash Password

↓

Database

↓

Login

↓

Verify Password

↓

JWT

↓

Authorization Header

↓

OAuth2PasswordBearer

↓

Current User

↓

Protected API
```

---

# Best Practices

* Keep authentication logic in reusable dependencies.
* Never decode JWT inside every router.
* Return 401 for invalid or expired tokens.
* Use `Depends()` instead of manually passing authentication information.

---

# Common Mistakes

### Decode JWT in every API

❌

Use

```python
Depends()
```

✔

---

### Trust `user_id` from Request Body

❌

Use

JWT

✔

---

### Use Email Instead of Current User

❌

JWT already identifies

user.

---

# Interview Questions

1. What is `OAuth2PasswordBearer`?
2. What does `Depends()` do?
3. Why use `get_current_user()`?
4. Why store `sub` in JWT?
5. What happens if JWT expires?
6. Why return `401 Unauthorized`?

---

# Summary

Today we implemented:

* ✅ OAuth2PasswordBearer
* ✅ JWT Decoding
* ✅ Current User Dependency
* ✅ Protected Endpoint (`/users/me`)
* ✅ Dependency Injection with `Depends()`

Your API can now identify the authenticated user from the JWT instead of trusting client-provided IDs.

---

# 🚀 Next Lesson (Module 5 — Part 6)

We'll refactor the **Skill Module** to use authentication properly.

Major changes:

* Remove `user_id` from `SkillCreate`
* Remove `user_id` from `SkillUpdate`
* Automatically assign `current_user["_id"]`
* Protect all Skill APIs
* Users can only create skills for themselves
* Foundation for ownership checks and role-based authorization

This is where SkillHub becomes a true multi-user application.
