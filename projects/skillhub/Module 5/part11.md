Excellent. This is where we'll make the course **production-grade**.

Up to now, our Refresh Token implementation is **stateless**.

That means:

```text
JWT Exists

↓

Valid Until Expiration
```

Even if the user clicks **Logout**, the JWT is still valid until it expires.

This is **not how production systems work**.

---

# Module 5 — Part 11

# Production Logout & Refresh Token Revocation

---

# Learning Objectives

After this lesson, you'll understand:

* Why Logout is difficult with JWT
* Stateless vs Stateful Authentication
* Refresh Token Storage
* Token Revocation
* Logout
* Logout from All Devices

---

# Current Authentication

```text
Login

↓

Access Token

↓

Refresh Token

↓

Protected APIs
```

Logout

↓

Nothing Happens.

Because JWT is still valid.

---

# Why?

JWT is

Self-contained.

Backend

Doesn't remember

JWT.

So

Cannot

Delete

It.

---

# Visual

```text
Client

↓

JWT

↓

Backend

↓

Verify Signature

↓

Valid
```

Backend

Never

Checks

Database.

---

# Problem

Suppose

Laptop

Stolen.

Attacker

Has

Refresh Token.

Even if

User

Clicks

Logout

Attacker

Can still

Generate

New Access Tokens.

Very dangerous.

---

# Solution

Store

Refresh Tokens

in Database.

---

# New Authentication Flow

```text
Login

↓

Generate Refresh Token

↓

Save Refresh Token

↓

MongoDB

↓

Return Tokens
```

---

Later

Refresh

↓

Check Database

↓

Exists?

↓

Generate Access Token.

---

Logout

↓

Delete Refresh Token

↓

Cannot Refresh Again.

---

# Updated Project Structure

```text
app/

├── models/
│     refresh_token.py ⭐
│
├── repositories/
│     refresh_repository.py ⭐
│
├── services/
│     auth_service.py
```

---

# MongoDB Collection

```text
refresh_tokens
```

Document

```json
{
    "_id": ObjectId(),

    "user_id": ObjectId(),

    "token_hash":"abcd1234...",

    "expires_at":"2026-08-01T12:00",

    "created_at":"2026-07-15T10:00"
}
```

Notice

We store

Hash.

Not

Actual Token.

Exactly like

Passwords.

---

# Why Hash Refresh Tokens?

Suppose

Database

Leaked.

Bad

```json
{
"refresh_token":"eyJhbGc..."
}
```

Attacker

Can Login.

---

Good

```json
{
"token_hash":"$2b$12..."
}
```

Cannot use.

Exactly like

Passwords.

---

# Login Flow

```text
User

↓

Login

↓

Generate Refresh Token

↓

Hash Refresh Token

↓

Store Hash

↓

Return Original Token
```

---

# Refresh Flow

Client Sends

Refresh Token.

↓

Hash

↓

Compare

Database Hash.

↓

Valid?

↓

Generate New Access Token.

---

# Logout

Current

```text
Client

↓

Delete Token
```

Backend

Knows Nothing.

---

New

```text
Logout

↓

Delete Refresh Token

↓

Database

↓

Cannot Refresh
```

Perfect.

---

# Logout All Devices

Suppose

Phone

Laptop

Tablet

Each

Has

Refresh Token.

Database

```text
User

↓

Phone

↓

Laptop

↓

Tablet
```

Logout

Only

Laptop

↓

Delete

Laptop Token.

Others

Still Work.

---

Logout

All Devices

↓

Delete

Every

Refresh Token

for User.

---

# Repository

Later

We'll create

```python
create_refresh_token()
```

```python
get_refresh_token()
```

```python
delete_refresh_token()
```

```python
delete_all_user_tokens()
```

---

# Authentication Architecture

```text
Register

↓

Hash Password

↓

Database

↓

Login

↓

JWT

↓

Hash Refresh Token

↓

Database

↓

Protected APIs

↓

Refresh

↓

Logout
```

---

# Access Token

Still

Stateless.

Never Stored.

---

Refresh Token

Stateful.

Stored.

---

Why?

Access

Expires

Quickly.

Refresh

Lives

Long.

Needs

Control.

---

# Real Companies

Google

Stores

Refresh Tokens.

---

Microsoft

Stores

Refresh Tokens.

---

GitHub

Stores

Refresh Tokens.

---

Banking Apps

Always

Store

Refresh Tokens.

---

# Token Rotation

Even Better.

Refresh

↓

Delete Old Refresh Token

↓

Generate New Refresh Token

↓

Store New Hash

↓

Return New Tokens.

Attacker

Cannot

Reuse

Old Token.

---

Visual

```text
Refresh

↓

Old Token

Deleted

↓

New Token

Created
```

---

# JWT Lifecycle

```text
Login

↓

Access

15 min

↓

Refresh

7 Days

↓

Logout

↓

Delete Refresh Token

↓

Finished
```

---

# Database

Collections

```text
users
```

```text
skills
```

```text
refresh_tokens
```

---

# Best Practices

* Never store raw Refresh Tokens.
* Store only hashed Refresh Tokens.
* Delete Refresh Tokens on logout.
* Support logout from a single device and all devices.
* Rotate Refresh Tokens after every refresh request.
* Add a TTL index to automatically remove expired refresh tokens.

Example MongoDB TTL index:

```javascript
db.refresh_tokens.createIndex(
  { expires_at: 1 },
  { expireAfterSeconds: 0 }
)
```

This lets MongoDB automatically remove expired token documents.

---

# Common Mistakes

❌ Store raw Refresh Tokens.

✔ Store hashed tokens.

---

❌ Logout

Deletes

Only

Client Token.

✔

Delete

Database Token.

---

❌

Never

Rotate.

✔

Rotate

Every Refresh.

---

# Authentication Progress

✅ Registration

✅ Login

✅ JWT

✅ OAuth2

✅ Current User

✅ Ownership

✅ Refresh Token

✅ Logout Design

⬜ RBAC

⬜ Admin

⬜ Permissions

---

# Interview Questions

1. Why can't JWT Access Tokens simply be "deleted" on the server?
2. Why store Refresh Tokens but not Access Tokens?
3. Why hash Refresh Tokens?
4. What is Refresh Token Rotation?
5. What is Logout from All Devices?
6. What is a MongoDB TTL index?

---

# Summary

Today we completed the **authentication lifecycle design**.

We covered:

* ✅ Production Logout
* ✅ Refresh Token Revocation
* ✅ Refresh Token Storage
* ✅ Token Hashing
* ✅ Logout from One Device
* ✅ Logout from All Devices
* ✅ Refresh Token Rotation
* ✅ Automatic Cleanup with TTL Index

At this point, you understand the architecture used in production systems like Google, GitHub, and Microsoft.

---

# 🚀 Next Module (Module 6 — Authorization & RBAC)

Authentication answers:

```text
Who are you?
```

Authorization answers:

```text
What are you allowed to do?
```

In the next module, we'll implement:

* User Roles (`ADMIN`, `USER`)
* Role-Based Access Control (RBAC)
* Permission decorators
* Admin-only APIs
* Role validation with `Depends()`
* Protecting routes based on roles
* Real-world authorization patterns used in enterprise FastAPI applications

This will complete the security layer of the SkillHub backend before we move on to advanced MongoDB topics.
