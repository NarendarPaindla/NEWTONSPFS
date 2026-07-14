Excellent. This is one of the **most important lessons** in backend development.

Many developers know how to copy JWT code from tutorials.

Very few actually understand **why** it works.

In this lesson, we will **not write code**.

Instead, we'll build a strong mental model. Once you understand this, JWT implementation becomes very easy.

---

# Module 5 — Part 1

# Authentication Fundamentals

---

# Learning Objectives

After this lesson, you'll understand:

* Authentication
* Authorization
* Identification
* Why passwords must not be stored in plain text
* Hashing
* Encryption
* Salting
* JWT
* OAuth2
* Bearer Token
* Login Flow
* Request Flow

---

# Real World Example

Imagine you're entering a college.

At the gate

Security asks

```text
Who are you?
```

You show

```text
College ID Card
```

Security checks

```text
ID Valid?

↓

Yes

↓

Enter
```

This is Authentication.

---

After entering

You try entering

Principal's Cabin.

Security asks

```text
Are you Principal?
```

No.

Access Denied.

This is Authorization.

---

# Authentication

Authentication answers

```text
WHO ARE YOU?
```

Example

```text
Email

+

Password
```

or

```text
Username

+

Password
```

or

```text
Google Login
```

---

# Authorization

Authorization answers

```text
WHAT ARE YOU ALLOWED TO DO?
```

Example

Student

```text
View Profile

View Marks
```

Teacher

```text
Update Marks

Create Assignments
```

Admin

```text
Delete Users

Manage System
```

Same login system.

Different permissions.

---

# Visual

```text
Authentication

↓

Identity Verified

↓

Authorization

↓

Permission Checked
```

---

# Identification

There are actually three steps.

```text
Identification

↓

Authentication

↓

Authorization
```

---

Identification

User says

```text
My email is

narendar@gmail.com
```

Backend now knows

Which user.

---

Authentication

Backend asks

```text
Password?
```

---

Authorization

Backend asks

```text
Allowed?

YES

NO
```

---

# Why Passwords Must Not Be Stored

Suppose database contains

```text
Email

Password
```

Bad database

```text
narendar@gmail.com

123456
```

Imagine hacker steals database.

They immediately know

Every password.

Disaster.

---

# Better Way

Never store

```text
123456
```

Store

```text
$2b$12$y8F...
```

Looks random.

This is

Hash.

---

# Hashing

Hashing

Converts

```text
Password
```

↓

Random String

Example

```text
hello123
```

↓

```text
$2b$12$kM1J...
```

Impossible to read.

---

# Important Property

Hashing

One Way

```text
Password

↓

Hash
```

Cannot go back.

---

Visual

```text
hello123

↓

Hash

↓

$2b$12$......

↓

Cannot Recover

hello123
```

---

# Encryption

Encryption

Can be reversed.

```text
Original

↓

Encrypted

↓

Decrypted

↓

Original
```

Need

Key.

---

# Difference

Hashing

```text
One Way
```

Encryption

```text
Two Way
```

---

Question

Passwords

Should use

Hash

or

Encryption?

Answer

Hashing.

---

# Why?

Backend never needs

Original password.

Need only

Comparison.

---

# Login Flow

Registration

User enters

```text
hello123
```

↓

Hash

↓

Database

Stores

```text
$2b$12$......
```

---

Later

Login

User enters

```text
hello123
```

↓

Hash again

↓

Compare

Hashes.

---

Visual

```text
Registration

Password

↓

Hash

↓

Database
```

Later

```text
Login

Password

↓

Hash

↓

Database Hash

↓

Equal?

↓

Login
```

Backend never stores

Real password.

---

# What Is Salt?

Question

Suppose

Two users

Choose

```text
password123
```

Without Salt

Both hashes

Same.

Bad.

---

Salt

Adds

Randomness.

Example

User1

```text
password123
```

↓

Hash

```text
ABC123
```

User2

```text
password123
```

↓

Hash

```text
XYZ789
```

Same password.

Different hash.

Very secure.

---

Good libraries

Automatically

Generate salt.

We don't write it manually.

---

# What Is JWT?

Suppose

Login Successful.

Should user send

Email

Password

Every request?

Imagine

```http
GET /skills
```

Need

Email

Password.

Again

```http
POST /skills
```

Need

Email

Password.

Terrible.

---

Solution

JWT.

---

JWT Means

```text
JSON

Web

Token
```

Backend gives

Small Token.

---

Example

```text
eyJhbGc...
```

Looks random.

Actually

Contains

User information.

---

# Login Flow

```text
Email

Password

↓

Server

↓

Verified

↓

JWT Token

↓

Client Stores Token
```

Next Request

```text
Authorization

Bearer

Token
```

Backend

Reads Token.

Knows User.

---

# JWT Structure

JWT has

Three Parts

```text
Header

.

Payload

.

Signature
```

Visual

```text
xxxxx

.

yyyyy

.

zzzzz
```

Three sections.

Separated by dots.

---

# Header

Contains

Algorithm.

Example

```json
{
"alg":"HS256",

"typ":"JWT"
}
```

---

# Payload

Contains

User Data.

Example

```json
{
"user_id":"6872...",

"email":"narendar@gmail.com"
}
```

Also contains

Expiry.

---

# Signature

Most important.

Prevents

Token modification.

If someone changes

Payload

↓

Signature Invalid

↓

Token Rejected.

---

# JWT Flow

```text
Login

↓

Verify Password

↓

Generate JWT

↓

Return Token

↓

Client Stores Token

↓

Every Request

↓

Authorization Header

↓

Backend Reads Token

↓

Current User
```

---

# OAuth2

Question

Who sends token?

Client.

How?

HTTP Header.

OAuth2 defines

Standard way.

Header

```http
Authorization:

Bearer eyJhbGc...
```

FastAPI

Provides

```python
OAuth2PasswordBearer
```

Reads

Header

Automatically.

---

# Bearer Token

Example

```http
GET /skills
```

Header

```text
Authorization:

Bearer

eyJhbGc...
```

Backend

Reads

Token.

Knows

Current User.

---

# Complete Authentication Flow

```text
Register

↓

Hash Password

↓

Store Hash

↓

Login

↓

Verify Password

↓

Generate JWT

↓

Client Stores JWT

↓

Protected API

↓

Authorization Header

↓

Backend Reads JWT

↓

Current User

↓

Database
```

---

# Common Misconceptions

### JWT stores the password

❌ No

JWT should never contain the password.

---

### JWT is encrypted

❌ No

JWT is signed, not encrypted by default.

The payload is Base64URL encoded, which means anyone holding the token can decode and read its contents. They **cannot safely modify it**, because the signature would no longer be valid.

Never put sensitive information (like passwords) inside a JWT payload.

---

### Hash can be reversed

❌ No

Good password hashes are one-way.

---

### Encryption and Hashing are the same

❌ No

Very different.

---

# Interview Questions

1. Authentication vs Authorization?
2. Why not store passwords?
3. Hashing vs Encryption?
4. What is Salt?
5. What is JWT?
6. JWT Structure?
7. Why use JWT?
8. What is Bearer Token?
9. What does OAuth2PasswordBearer do?
10. Why should passwords never be included in JWTs?

---

# Summary

Today you learned

* ✅ Authentication
* ✅ Authorization
* ✅ Identification
* ✅ Password Hashing
* ✅ Encryption
* ✅ Salting
* ✅ JWT
* ✅ OAuth2
* ✅ Bearer Token
* ✅ Complete Authentication Flow

You now have the conceptual foundation needed to implement secure authentication correctly.

---

# 🚀 Next Lesson (Module 5 — Part 2)

Now we'll start coding.

We'll implement:

* Install authentication libraries
* Create `core/security.py`
* Configure Passlib
* Configure bcrypt
* Configure JWT settings
* Generate password hashes
* Verify passwords

This is where SkillHub becomes a secure application by introducing proper password handling before we implement login and token generation.
