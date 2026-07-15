Excellent. This is one of the most important lessons in the entire course.

After this lesson, your application will have a **working Login API** that generates a JWT Access Token.

> **Architectural improvement:** Instead of adding login to `users.py`, we'll create a dedicated `auth.py` router. Authentication is a separate concern from user management, and this structure scales much better.

---

# Module 5 — Part 4

# Login API + JWT Token Generation

---

# Learning Objectives

By the end of this lesson, you'll build:

* Login Request Schema
* Auth Router
* User Lookup
* Password Verification
* JWT Generation
* Access Token Response

---

# Authentication Flow

```text
User

↓

POST /auth/login

↓

Email + Password

↓

Find User

↓

Verify Password

↓

Create JWT

↓

Return Token

↓

Future Requests
```

---

# Updated Project Structure

```text
app/

├── api/
│     auth.py      ⭐ NEW
│     users.py
│     skills.py
│
├── schemas/
│     auth_schema.py ⭐ NEW
│     user_schema.py
│
├── services/
│     auth_service.py ⭐ NEW
│     user_service.py
│
├── repositories/
│     user_repository.py
│
└── core/
      security.py
```

This separation makes the project easier to maintain.

---

# Step 1 — Create `app/schemas/auth_schema.py`

```python
from pydantic import BaseModel
from pydantic import EmailStr


class LoginRequest(BaseModel):

    email: EmailStr

    password: str


class TokenResponse(BaseModel):

    access_token: str

    token_type: str = "bearer"
```

---

# Why a Separate Schema?

Registration

```text
POST /users
```

Login

```text
POST /auth/login
```

Different APIs.

Different request bodies.

---

# Step 2 — Update `user_repository.py`

Add this method.

```python
async def get_by_email(
    self,
    email: str
):
    return await database.users.find_one(
        {
            "email": email
        }
    )
```

If you already have this method from previous lessons, **don't duplicate it**.

---

# Step 3 — Create `app/services/auth_service.py`

Imports

```python
from fastapi import HTTPException
from fastapi import status

from app.repositories.user_repository import (
    user_repository
)

from app.core.security import (
    verify_password,
    create_access_token
)
```

---

## Create Service

```python
class AuthService:

    async def login(
        self,
        email: str,
        password: str
    ):
```

---

## Find User

```python
        user = await user_repository.get_by_email(
            email
        )

        if user is None:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Invalid email or password"
            )
```

Notice

We do **not** say

```text
Email Not Found
```

Why?

Security.

Attackers should never know whether an email exists.

---

## Verify Password

```python
        is_valid = verify_password(
            password,
            user["password"]
        )

        if not is_valid:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Invalid email or password"
            )
```

Again

Same error.

---

## Create Token

```python
        access_token = create_access_token(
            {
                "sub": str(user["_id"])
            }
        )
```

We store

```text
sub
```

which means

Subject.

We'll use this later to identify the logged-in user.

---

## Return Response

```python
        return {

            "access_token": access_token,

            "token_type": "bearer"

        }
```

---

## Complete Service

```python
class AuthService:

    async def login(
        self,
        email: str,
        password: str
    ):

        user = await user_repository.get_by_email(
            email
        )

        if user is None:
            raise HTTPException(
                status_code=401,
                detail="Invalid email or password"
            )

        if not verify_password(
            password,
            user["password"]
        ):
            raise HTTPException(
                status_code=401,
                detail="Invalid email or password"
            )

        access_token = create_access_token(
            {
                "sub": str(user["_id"])
            }
        )

        return {

            "access_token": access_token,

            "token_type": "bearer"

        }


auth_service = AuthService()
```

---

# Step 4 — Create `app/api/auth.py`

```python
from fastapi import APIRouter

from app.core.config import settings

from app.schemas.auth_schema import (
    LoginRequest
)

from app.services.auth_service import (
    auth_service
)

router = APIRouter(

    prefix=f"{settings.api_prefix}/auth",

    tags=["Authentication"]

)
```

---

# Login API

```python
@router.post("/login")
async def login(
    credentials: LoginRequest
):

    token = await auth_service.login(

        credentials.email,

        credentials.password

    )

    return token
```

---

# Step 5 — Register Router

Update `main.py`

```python
from app.api.auth import (
    router as auth_router
)

app.include_router(auth_router)
```

---

# Swagger

Now you'll see

```text
Authentication

↓

POST

/api/v1/auth/login
```

---

# Request

```json
{
    "email":"narendar@gmail.com",

    "password":"hello123"
}
```

---

# Response

```json
{
    "access_token":"eyJhbGc...",

    "token_type":"bearer"
}
```

JWT Generated.

---

# Complete Login Flow

```text
Client

↓

POST /auth/login

↓

Find User

↓

Verify Password

↓

Create JWT

↓

Return Token

↓

Client Stores Token
```

---

# What is `"sub"`?

We stored

```python
{
    "sub": user_id
}
```

Why?

Later

Protected APIs

will decode JWT

↓

Read

```text
sub
```

↓

Know

Current User.

No database lookup by email is needed to identify the authenticated user.

---

# MongoDB

Nothing changes.

Database still contains

```json
{
    "_id": ObjectId(...),

    "email":"narendar@gmail.com",

    "password":"$2b$12$..."
}
```

---

# Current Progress

✅ Registration

✅ Password Hashing

✅ Login

✅ JWT Generation

⬜ Read JWT

⬜ Current User

⬜ Protected APIs

⬜ OAuth2

⬜ Refresh Token

⬜ Logout

---

# Best Practices

* Keep authentication separate from user management.
* Return the same error for invalid email and invalid password.
* Store only the user identifier (`sub`) inside the token.
* Never include passwords or password hashes in JWT payloads.

---

# Interview Questions

1. Why return the same error for invalid email and password?
2. What is the purpose of the `sub` claim?
3. Why create a separate `auth.py` router?
4. Why store the user ID instead of the email in the JWT?
5. What should never be included in a JWT payload?

---

# Summary

Today we built a production-style login system.

We now have:

* ✅ Authentication Router
* ✅ Login Schema
* ✅ Auth Service
* ✅ Password Verification
* ✅ JWT Access Token Generation

Users can now register, log in, and receive a valid JWT token.

---

# 🚀 Next Lesson (Module 5 — Part 5)

We'll implement **OAuth2PasswordBearer** and `get_current_user()`.

You'll learn how to:

* Read the JWT from the `Authorization` header
* Decode and validate the token
* Extract the logged-in user's ID
* Load the current user from MongoDB
* Protect APIs using `Depends()`

After that, SkillHub will support **authenticated routes**, and we'll remove `user_id` from the Skill creation API entirely.
