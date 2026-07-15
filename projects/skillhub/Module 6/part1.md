Excellent. Since we've completed the **Authentication module** of the **SkillHub FastAPI + MongoDB** project, let's move to the **next major module**.

---

# Module 6 — Authorization, Middleware & Security

> **Course Goal**
>
> Transform SkillHub from an authenticated application into a **production-ready secure backend**.

---

# What We Will Build

By the end of this module, SkillHub will support:

* ✅ Role-Based Access Control (RBAC)
* ✅ Admin & User Roles
* ✅ Permission-Based APIs
* ✅ Custom Dependencies
* ✅ CORS Configuration
* ✅ Custom Middleware
* ✅ Request Logging Middleware
* ✅ Security Headers
* ✅ Rate Limiting Basics
* ✅ Common Security Vulnerabilities
* ✅ Production Security Best Practices

---

# Module Roadmap

## Part 1 — User Roles (Today)

We'll implement:

* What is Authorization?
* What is RBAC?
* Add `role` field to User
* Define User Roles
* Update Registration
* Default Role
* Why Enums are useful

---

## Part 2

Role Dependency

We'll build

```python
get_current_admin()
```

using

```python
Depends()
```

---

## Part 3

Admin APIs

Example

```
GET /users

DELETE /users/{id}

GET /analytics
```

Only Admin can access.

---

## Part 4

Permission System

Instead of checking

```
Admin
```

We'll check

```
Permission
```

Example

```
can_delete_users

can_view_reports

can_manage_skills
```

---

## Part 5

Middleware

Build

```python
Request Logging Middleware
```

Logs

```
Method

Path

Response Time

Status Code
```

---

## Part 6

CORS

Understand

```
Origin

Preflight

OPTIONS

Credentials
```

Configure FastAPI properly.

---

## Part 7

Security Headers

We'll add

```
X-Frame-Options

X-Content-Type-Options

Referrer-Policy

Cache-Control
```

---

## Part 8

Rate Limiting Basics

Protect

```
/login
```

Against

Brute Force Attacks.

---

## Part 9

Security Vulnerabilities

We'll discuss

* SQL Injection
* NoSQL Injection
* XSS
* CSRF
* JWT Theft
* Password Attacks
* Broken Authentication
* Broken Authorization

---

## Part 10

Production Security Checklist

Everything a backend should have before deployment.

---

# Folder Structure After Module 6

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
│     permissions.py      ⭐ NEW
│
├── middleware/
│     logging.py          ⭐ NEW
│
├── enums/
│     roles.py            ⭐ NEW
│
├── schemas/
│
├── services/
│
├── repositories/
│
└── main.py
```

---

# What We'll Learn First

Before writing code, we need to understand

```
Authentication

vs

Authorization
```

You already know

Authentication

↓

```
Who are you?
```

Authorization

↓

```
What are you allowed to do?
```

Today we'll implement

```
User Roles
```

---

# Real World Example

## Student Portal

Student

Can

```
View Marks

Update Profile
```

Cannot

```
Delete Student

Add Faculty
```

---

Faculty

Can

```
Upload Marks

View Students
```

Cannot

```
Delete College
```

---

Admin

Can

Everything.

---

# Role-Based Access Control (RBAC)

Instead of writing

```python
if email == "admin@gmail.com":
```

We assign

```
Role
```

to every user.

Example

```json
{
    "full_name": "Narendar",
    "email": "narendar@gmail.com",
    "role": "USER"
}
```

Admin

```json
{
    "full_name": "Admin",
    "email": "admin@skillhub.com",
    "role": "ADMIN"
}
```

---

# Why Use Roles?

Without Roles

```text
100 APIs

↓

100 if statements
```

Messy.

---

With Roles

```text
Depends(get_current_admin)
```

Done.

---

# Role Types We'll Use

```text
ADMIN

USER
```

Simple.

Later

Can extend to

```
MODERATOR

TRAINER

MENTOR
```

---

# MongoDB User Document

Current

```json
{
    "_id": ObjectId(),

    "full_name":"Narendar",

    "email":"narendar@gmail.com",

    "password":"..."
}
```

After Module 6

```json
{
    "_id": ObjectId(),

    "full_name":"Narendar",

    "email":"narendar@gmail.com",

    "password":"...",

    "role":"USER"
}
```

---

# Why Default USER?

When

```
POST /users
```

Everyone

Should become

```
USER
```

Only Admin

Can promote

Someone

To

```
ADMIN
```

Never

Allow

```json
{
    "role":"ADMIN"
}
```

During registration.

Huge security issue.

---

# Architecture

```text
Register

↓

Role = USER

↓

Login

↓

JWT

↓

Current User

↓

Role Check

↓

API
```

---

# Best Practices

* Never allow users to choose their own role during registration.
* Store roles in the database.
* Check roles using reusable dependencies.
* Keep authorization separate from authentication.
* Default every new account to the least privileged role (`USER`).

---

# Interview Questions

1. Authentication vs Authorization?
2. What is RBAC?
3. Why use roles?
4. Why shouldn't users choose the `ADMIN` role during registration?
5. Why is `USER` the default role?

---

# Summary

Today we started **Module 6**.

We learned:

* ✅ Authorization
* ✅ RBAC
* ✅ User Roles
* ✅ Role Design
* ✅ Database Changes
* ✅ Why Roles Matter

---

# 🚀 Next Lesson (Module 6 — Part 2)

We'll start coding by implementing:

* `app/enums/roles.py`
* Update `user_schema.py`
* Update `user_service.py`
* Automatically assign the `USER` role during registration
* Store roles in MongoDB

From that point onward, every user in SkillHub will have a secure role, laying the foundation for **Admin-only APIs** and **permission-based authorization**.
