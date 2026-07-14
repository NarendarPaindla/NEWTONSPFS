Excellent. Now we start writing **real authentication code**.

This is one of the most important files in the entire backend because every secure API will use it.

We'll build it professionally, but keep it beginner-friendly.

---

# Module 5 — Part 2

# Authentication Setup + Security Configuration

> **Goal**
>
> Build one reusable file that handles:
>
> * Password Hashing
> * Password Verification
> * JWT Creation
> * JWT Verification (later)
> * Token Expiration Configuration

---

# Step 1 — Install Packages

```bash
pip install "passlib[bcrypt]"
pip install "python-jose[cryptography]"
pip install python-multipart
```

Freeze requirements

```bash
pip freeze > requirements.txt
```

---

# Updated requirements.txt

```text
fastapi
uvicorn
motor
pymongo
pydantic-settings
python-dotenv
email-validator

passlib[bcrypt]
python-jose[cryptography]
python-multipart
```

---

# Why These Packages?

## passlib

Professional password hashing library.

We never hash passwords manually.

---

## bcrypt

The hashing algorithm.

Industry standard.

---

## python-jose

Used to

* Create JWT
* Verify JWT
* Decode JWT

---

## python-multipart

Needed for

```text
OAuth2PasswordRequestForm
```

during Login.

---

# Updated Project Structure

```text
app/

├── api/

├── core/
│     config.py
│     security.py      ⭐ NEW
│
├── db/

├── repositories/

├── schemas/

├── services/

├── utils/

└── main.py
```

---

# Step 2 — Update `.env`

Add these values.

```env
PROJECT_NAME=SkillHub API
PROJECT_VERSION=1.0.0

DEBUG=True

API_PREFIX=/api/v1

MONGODB_URL=mongodb://localhost:27017
DATABASE_NAME=skillhub_db

SECRET_KEY=your_super_secret_key_change_this_in_production

ALGORITHM=HS256

ACCESS_TOKEN_EXPIRE_MINUTES=30
```

---

# What are these?

## SECRET_KEY

Used to sign JWT.

Very important.

Never expose it publicly.

Never upload real production secrets to GitHub.

---

## ALGORITHM

JWT Signing Algorithm

We'll use

```text
HS256
```

Industry standard.

---

## ACCESS_TOKEN_EXPIRE_MINUTES

```text
30
```

means

JWT expires after

30 minutes.

---

# Step 3 — Update `config.py`

```python
from pydantic_settings import BaseSettings
from pydantic_settings import SettingsConfigDict


class Settings(BaseSettings):

    project_name: str

    project_version: str

    debug: bool

    api_prefix: str

    mongodb_url: str

    database_name: str

    secret_key: str

    algorithm: str

    access_token_expire_minutes: int

    model_config = SettingsConfigDict(
        env_file=".env",
        extra="ignore"
    )


settings = Settings()
```

Now authentication settings are available everywhere.

---

# Step 4 — Create `app/core/security.py`

This file becomes the heart of authentication.

---

## Imports

```python
from datetime import datetime
from datetime import timedelta
from datetime import timezone

from jose import jwt

from passlib.context import CryptContext

from app.core.config import settings
```

---

# Step 5 — Password Context

```python
pwd_context = CryptContext(

    schemes=["bcrypt"],

    deprecated="auto"

)
```

---

# What is CryptContext?

Instead of calling bcrypt directly,

Passlib manages hashing for us.

Benefits

* Easy upgrades
* Multiple algorithms
* Cleaner code

---

Visual

```text
Password

↓

Passlib

↓

bcrypt

↓

Hash
```

---

# Step 6 — Hash Password

```python
def hash_password(
    password: str
) -> str:

    return pwd_context.hash(
        password
    )
```

---

Example

Input

```text
hello123
```

Output

```text
$2b$12$wJ3K....
```

Notice

Looks random.

Very secure.

---

# Step 7 — Verify Password

```python
def verify_password(

    plain_password: str,

    hashed_password: str

) -> bool:

    return pwd_context.verify(

        plain_password,

        hashed_password

    )
```

---

Question

Why not compare

```python
==
```

?

Wrong.

Hashes contain salts.

Passlib verifies correctly.

---

Visual

```text
User Password

↓

verify()

↓

Stored Hash

↓

True

or

False
```

---

# Step 8 — Create JWT

```python
def create_access_token(

    data: dict

):
```

---

Copy Data

```python
    to_encode = data.copy()
```

Never modify

Original dictionary.

---

# Expiration

```python
    expire = datetime.now(

        timezone.utc

    ) + timedelta(

        minutes=settings.access_token_expire_minutes

    )
```

Example

Current

```text
10:00
```

Expire

```text
10:30
```

---

Add Expiry

```python
    to_encode.update(

        {

            "exp": expire

        }

    )
```

JWT now knows

When to expire.

---

Encode

```python
    encoded_jwt = jwt.encode(

        to_encode,

        settings.secret_key,

        algorithm=settings.algorithm

    )
```

Return

```python
    return encoded_jwt
```

---

# Complete File

```python
from datetime import datetime
from datetime import timedelta
from datetime import timezone

from jose import jwt

from passlib.context import CryptContext

from app.core.config import settings


pwd_context = CryptContext(

    schemes=["bcrypt"],

    deprecated="auto"

)


def hash_password(
    password: str
):

    return pwd_context.hash(
        password
    )


def verify_password(

    plain_password,

    hashed_password

):

    return pwd_context.verify(

        plain_password,

        hashed_password

    )


def create_access_token(
    data: dict
):

    to_encode = data.copy()

    expire = datetime.now(
        timezone.utc
    ) + timedelta(
        minutes=settings.access_token_expire_minutes
    )

    to_encode.update(
        {
            "exp": expire
        }
    )

    encoded_jwt = jwt.encode(

        to_encode,

        settings.secret_key,

        algorithm=settings.algorithm

    )

    return encoded_jwt
```

---

# Current Flow

```text
Password

↓

hash_password()

↓

Database
```

Later

```text
Login

↓

verify_password()

↓

Correct?

↓

create_access_token()

↓

JWT
```

---

# Test Password Hash

Open Python shell

```python
from app.core.security import hash_password

print(
    hash_password(
        "hello123"
    )
)
```

Output

```text
$2b$12$.....
```

Every run

Different hash.

Because

Salt.

---

# Test Verify

```python
from app.core.security import (
    hash_password,
    verify_password
)

hashed = hash_password(
    "hello123"
)

print(
    verify_password(
        "hello123",
        hashed
    )
)
```

Output

```text
True
```

Wrong password

```text
False
```

---

# Test JWT

```python
from app.core.security import create_access_token

token = create_access_token(

    {

        "sub": "narendar@gmail.com"

    }

)

print(token)
```

Output

```text
eyJhbGc.....
```

JWT Generated.

---

# Best Practices

* Never store plain-text passwords.
* Never write your own hashing algorithm.
* Always use Passlib with bcrypt (or another well-established password hashing algorithm).
* Keep JWT settings in `.env`.
* Use UTC timestamps for token expiration.

---

# Common Mistakes

### Store Password

```text
123456
```

❌

---

Store

```text
$2b$12....
```

✔

---

### Secret Key

```text
abc123
```

Weak.

Use

Long

Random

Secret.

---

### Forget Expiration

Bad.

Tokens should expire.

---

# Interview Questions

1. Why use Passlib?
2. What is CryptContext?
3. Why use bcrypt?
4. Why not compare passwords using `==`?
5. Why use UTC for expiration?
6. What does `"exp"` mean in JWT?

---

# Summary

Today we built our **security foundation**.

We implemented:

* ✅ Authentication libraries
* ✅ JWT configuration
* ✅ Password hashing
* ✅ Password verification
* ✅ Access token generation
* ✅ Security settings

---

# 🚀 Next Lesson (Module 5 — Part 3)

Now we'll integrate this into our project.

We'll update:

* User Schema
* User Repository
* User Service
* Registration API

so that:

```text
User Registers

↓

Password

↓

Hash

↓

MongoDB
```

Instead of storing plain-text passwords, SkillHub will securely store hashed passwords and be ready for the Login API.
