Excellent. This is the point where **SkillHub** transforms from a public CRUD application into a **secure production-ready backend**.

One important architectural change before we start:

> Up to now, clients send `user_id` when creating a skill.

Example:

```json
{
    "name": "Python",
    "category": "Backend",
    "user_id": "6872abc..."
}
```

This is **not secure** because any client could provide someone else's `user_id` and create data on their behalf.

After authentication, the client **will no longer send `user_id`**. Instead:

```text
Login
    │
    ▼
JWT Access Token
    │
    ▼
Backend extracts user_id from the token
    │
    ▼
Skill automatically belongs to the logged-in user
```

This is how production APIs work.

---

# Module 5 — Authentication & Authorization

## Learning Objectives

By the end of this module, you'll be able to build:

* User Registration
* Secure Password Storage
* Password Hashing
* Password Verification
* Login
* JWT Authentication
* OAuth2 Authentication
* Protected APIs
* Current Logged-in User
* Token Expiry
* Refresh Tokens
* Logout
* Role-Based Authorization (later)

---

# Authentication Flow

```text
Register
      │
      ▼
Password Hashing
      │
      ▼
MongoDB
      │
      ▼
Login
      │
      ▼
Password Verification
      │
      ▼
JWT Token
      │
      ▼
Protected APIs
```

---

# Module Roadmap

## Part 1

Authentication Fundamentals

* Authentication vs Authorization
* Password Hashing
* Why plain-text passwords are dangerous
* JWT Introduction
* OAuth2 Flow

---

## Part 2

Install Authentication Libraries

* passlib
* bcrypt
* python-jose
* python-multipart

---

## Part 3

Update User Schema

Add

```python
password
```

Registration Model

---

## Part 4

Password Hashing

Using

```python
passlib
```

---

## Part 5

Registration API

Store

Hashed Password

NOT

Plain Password

---

## Part 6

Password Verification

Verify

```text
Entered Password

↓

Stored Hash
```

---

## Part 7

JWT Token Generation

Create

Access Token

---

## Part 8

Login API

Returns

```json
{
    "access_token":"...",

    "token_type":"bearer"
}
```

---

## Part 9

OAuth2PasswordBearer

Protected APIs

---

## Part 10

Current User

```python
get_current_user()
```

---

## Part 11

Remove user_id From Skill API

Current

```json
{
"user_id":"..."
}
```

New

```json
{
"name":"Python"
}
```

Backend

↓

Extracts

user_id

Automatically

---

## Part 12

Refresh Token

---

## Part 13

Logout

---

## Part 14

Role-Based Authorization

Admin

Normal User

---

# Folder Structure After Authentication

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
│     auth.py
│
├── repositories/
│
├── services/
│
├── schemas/
│     auth_schema.py
│     user_schema.py
│
├── utils/
│
└── main.py
```

---

# New Packages

```bash
pip install passlib[bcrypt]
pip install python-jose[cryptography]
pip install python-multipart
```

Later

```bash
pip freeze > requirements.txt
```

---

# Updated Requirements

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

# Project Changes

Before Authentication

```text
Users

↓

Skills
```

Anyone

Can

Create Skills.

---

After Authentication

```text
Register

↓

Login

↓

JWT

↓

Current User

↓

Create Skill
```

Much more secure.

---

# What Will Change?

Current Skill Schema

```python
class SkillCreate(BaseModel):

    name:str

    category:str

    level:str

    experience:int

    user_id:str
```

After JWT

```python
class SkillCreate(BaseModel):

    name:str

    category:str

    level:str

    experience:int
```

Notice

```text
user_id
```

Removed.

Backend already knows

Who logged in.

---

# Authentication Flow

```text
Client

↓

POST /login

↓

Email + Password

↓

JWT

↓

Authorization Header

↓

Bearer Token

↓

Protected API

↓

Current User

↓

MongoDB
```

---

# Best Practices We'll Follow

* Never store plain-text passwords.
* Never return passwords in API responses.
* Hash passwords before saving.
* Use short-lived access tokens.
* Extract user identity from the token instead of trusting client-supplied IDs.
* Keep authentication logic separate from business logic.

---

# Summary

We have completed **SkillHub v1.0**.

Next, we'll build **SkillHub v2.0** with authentication.

We'll add:

* ✅ Secure Registration
* ✅ Password Hashing
* ✅ Login
* ✅ JWT Authentication
* ✅ Protected APIs
* ✅ Current User
* ✅ Automatic Skill Ownership
* ✅ Refresh Tokens
* ✅ Logout
* ✅ Authorization

---

# 🚀 Next Lesson (Module 5 — Part 1)

We'll begin with the **foundation of authentication**:

* Authentication vs Authorization
* Why passwords must never be stored as plain text
* Hashing vs Encryption
* What JWT is
* Structure of a JWT (`Header.Payload.Signature`)
* How authentication works end-to-end in a FastAPI application

This conceptual foundation will make the implementation in the following lessons much easier to understand.
