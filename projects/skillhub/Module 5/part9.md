Excellent. Now we are entering **advanced authentication**.

Most beginner tutorials stop after generating an Access Token.

Production systems use **two tokens**:

* Access Token
* Refresh Token

This provides both **security** and **good user experience**.

---

# Module 5 — Part 9

# Refresh Tokens

---

# Learning Objectives

After this lesson, you'll understand:

* Why Access Tokens expire
* What Refresh Tokens are
* Refresh Token Flow
* Why two tokens are better than one
* How production authentication works

---

# Problem

Suppose user logs in.

Backend returns

```text
Access Token
```

Expiry

```text
30 minutes
```

After

30 minutes

↓

Token expires.

Every API returns

```http
401 Unauthorized
```

Should user login again?

Imagine

Gmail

Asks

Login

Every

30 minutes.

Very annoying.

---

# Solution

Introduce

```text
Refresh Token
```

---

# Two Token Architecture

```text
Access Token

↓

30 Minutes
```

---

```text
Refresh Token

↓

7 Days
```

(or 30 days depending on the application).

---

# Why Two Tokens?

Access Token

```text
Short

Fast

Secure
```

Refresh Token

```text
Long

Only used

To create

New Access Token
```

---

# Visual

```text
Login

↓

Access Token

30 min

+

Refresh Token

7 days
```

---

# Request Flow

```text
Protected API

↓

Access Token

↓

Success
```

---

After

30 Minutes

```text
Access Token

Expired
```

↓

Client sends

```text
Refresh Token
```

↓

Backend

↓

New Access Token

↓

Continue.

No Login.

---

# Real Example

YouTube

Open

Morning.

Use

Whole Day.

Why

No Login?

Refresh Token.

---

# Banking Apps

Usually

Very short

Access Token

Example

```text
10 minutes
```

Refresh

```text
1 day
```

More secure.

---

# Access Token

Contains

```text
Current User

Expiry
```

Used

For

Every Request.

---

# Refresh Token

Contains

User Identity.

Used

Only

For

```http
POST /refresh
```

Nothing else.

---

# Authentication Flow

```text
Email

Password

↓

Login

↓

Access Token

+

Refresh Token

↓

Protected APIs
```

---

Later

```text
Access Token

Expired

↓

Refresh Token

↓

New Access Token
```

---

# JWT Lifetime

Current

```text
30 Minutes
```

Later

We'll configure

Access

```text
15 minutes
```

Refresh

```text
7 days
```

Industry standard.

---

# Why Not Make Access Token 30 Days?

Suppose

Laptop

Stolen.

Attacker

Gets

JWT.

Can use

30 days.

Very dangerous.

---

Short expiry

Limits

Damage.

---

# Why Refresh Token?

Without

Refresh

↓

Login Again.

Bad UX.

---

With

Refresh

↓

Automatic.

Good UX.

---

# Visual

```text
Access

Short

Secure
```

```text
Refresh

Long

Convenient
```

---

# Production Flow

```text
Login

↓

Access

15 min

↓

Refresh

7 Days
```

---

API

```text
Authorization

Bearer

Access
```

---

Expired

↓

```http
POST

/auth/refresh
```

↓

New Access Token

---

# Token Rotation

Even better.

Every Refresh

↓

Generate

New Refresh Token.

Old

Invalid.

Very secure.

We'll implement this later as an enhancement.

---

# Database Storage

There are two common approaches.

### Stateless (Simpler)

Only JWT.

Nothing stored.

---

### Stateful (More Secure)

Store

Refresh Token

(or its hash)

in database.

Can revoke.

We'll start with the simpler approach and discuss stateful refresh tokens later.

---

# Endpoint

We'll create

```http
POST

/api/v1/auth/refresh
```

Request

```json
{
"refresh_token":"..."
}
```

---

Response

```json
{
"access_token":"...",

"token_type":"bearer"
}
```

Simple.

---

# Logout

Current

JWT

Cannot be revoked.

Because

Stateless.

---

Later

We'll

Blacklist

Refresh Tokens

or

Delete them from the database.

---

# Authentication Timeline

```text
09:00

Login
```

↓

Access

```text
09:30

Expired
```

↓

Refresh

↓

New Access

```text
10:00
```

↓

Continue.

No Login.

---

# OAuth2 Flow

```text
Login

↓

Access Token

↓

API

↓

Expired

↓

Refresh Endpoint

↓

New Access Token
```

---

# Security Benefits

Short

Access

↓

Less damage

If stolen.

---

Refresh

↓

Better User Experience.

---

Together

↓

Production Authentication.

---

# Current Authentication Architecture

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

Protected APIs

↓

Refresh Token

↓

New JWT
```

---

# Best Practices

* Keep Access Tokens short-lived.
* Never use Refresh Tokens to access protected APIs directly.
* Use Refresh Tokens only to obtain new Access Tokens.
* Consider rotating Refresh Tokens for better security.
* In production, consider storing Refresh Tokens (or their hashes) to support revocation.

---

# Common Mistakes

### One Token

30 days

❌

---

### Access

15 min

Refresh

7 Days

✔

---

### Send Refresh

To

Protected API

❌

---

### Send Access

To

Protected API

✔

---

# Interview Questions

1. Why use Refresh Tokens?
2. Difference between Access and Refresh Tokens?
3. Why keep Access Tokens short-lived?
4. Can Refresh Tokens access protected APIs?
5. What is token rotation?
6. What is the difference between stateless and stateful refresh tokens?

---

# Summary

Today you learned the architecture behind production authentication:

* ✅ Access Tokens
* ✅ Refresh Tokens
* ✅ Token Expiration Strategy
* ✅ Refresh Flow
* ✅ Security Benefits
* ✅ Token Rotation Concept

At this point, you understand **why** modern applications use two-token authentication.

---

# 🚀 Next Lesson (Module 5 — Part 10)

We'll implement the code for Refresh Tokens.

You'll build:

* `create_refresh_token()`
* Update `security.py`
* Return both Access and Refresh Tokens during login
* `/auth/refresh` endpoint
* Generate a new Access Token from a valid Refresh Token

This will complete a production-style JWT authentication flow and prepare us for **Role-Based Access Control (RBAC)** in the next module.
