Excellent. This lesson fixes one of the biggest design problems in our current application.

Right now, our duplicate validation checks only:

```text
skill_name
```

That means:

```
User A
Python
```

and

```
User B
Python
```

❌ User B is rejected.

This is incorrect for a multi-user application.

---

# Module 5 — Part 8

# User-Specific Duplicate Validation

---

# Learning Objectives

After this lesson you'll understand:

* Why global uniqueness is wrong
* Compound uniqueness
* Multi-tenant applications
* Repository improvements
* Service improvements
* MongoDB compound indexes

---

# Current Design (Wrong)

Database

```
User A

↓

Python
```

User B

```
Python
```

Current validation

```python
get_by_name("Python")
```

↓

Already Exists

↓

Rejected

❌

---

# Desired Design

```
User A

↓

Python
```

```
User B

↓

Python
```

Allowed

✅

---

But

```
User A

↓

Python

Python
```

Rejected

❌

---

# Real World Examples

## GitHub

Repository

```
john

↓

portfolio
```

Another user

```
alice

↓

portfolio
```

Allowed.

---

## Gmail

Email

Must be globally unique.

```
abc@gmail.com
```

No duplicates.

---

## SkillHub

Skill names

Should only be unique

per user.

---

# Multi-Tenant Applications

This is called

```
Tenant Isolation
```

Each user owns

their own data.

Visual

```
User A

↓

Python

Java

React
```

```
User B

↓

Python

MongoDB
```

Same names

Different owners

Perfectly valid.

---

# Step 1 — Repository

Current

```python
async def get_by_name(
    self,
    name
)
```

Replace with

```python
async def get_by_name_and_user(
    self,
    name: str,
    user_id: ObjectId
):
```

---

Implementation

```python
async def get_by_name_and_user(
    self,
    name: str,
    user_id: ObjectId
):

    return await database.skills.find_one(

        {

            "name": name,

            "user_id": user_id

        }

    )
```

Now MongoDB checks

Both

```
name

+

user_id
```

---

# Visual

Current

```
name
```

↓

Duplicate

Wrong.

---

New

```
name

+

user_id
```

↓

Correct.

---

# Step 2 — Service

Current

```python
existing_skill = await skill_repository.get_by_name(
    skill.name
)
```

Replace

```python
existing_skill = await (
    skill_repository.get_by_name_and_user(

        skill.name,

        current_user["_id"]

    )
)
```

---

Duplicate

```python
if existing_skill:

    raise HTTPException(

        status_code=400,

        detail="You already have this skill"

    )
```

Much better message.

---

# Create Flow

```
Current User

↓

Python

↓

Repository

↓

Find

Name

+

User

↓

Duplicate?

↓

Insert
```

---

# Step 3 — Update Validation

Problem

Suppose

User

Updates

```
Python
```

↓

Python

Same record.

Should work.

---

But

Current check

Finds

Itself.

Wrong.

---

Solution

Repository

```python
async def get_duplicate_for_update(

    self,

    skill_id,

    user_id,

    name

)
```

---

MongoDB Query

```python
return await database.skills.find_one(

    {

        "_id": {

            "$ne": skill_id

        },

        "user_id": user_id,

        "name": name

    }

)
```

---

Meaning

```
NOT

Current Skill

AND

Same User

AND

Same Name
```

Perfect.

---

# Service

Before Update

```python
duplicate = await (
    skill_repository.get_duplicate_for_update(

        object_id,

        current_user["_id"],

        skill.name

    )
)
```

---

If exists

```python
raise HTTPException(

    status_code=400,

    detail="You already have this skill"

)
```

---

# MongoDB Compound Index

Current

No Index.

Slow.

---

Later

We'll create

```javascript
db.skills.createIndex(

{

"user_id":1,

"name":1

},

{

unique:true

}
)
```

Meaning

```
User

+

Skill

Must Be Unique
```

Database itself

Protects duplicates.

---

# Why Index?

Without

Every insert

↓

Scan

Entire collection.

---

With Index

```
Index

↓

Immediate Search
```

Much faster.

---

# Visual

```
Current

100000 Skills

↓

Scan All
```

---

Indexed

```
Index

↓

One Lookup
```

---

# Final Create Flow

```
JWT

↓

Current User

↓

Repository

↓

Find

user_id

+

name

↓

Exists?

↓

Insert
```

---

# Real Production Design

Current

```
Python

Unique
```

Wrong.

---

Production

```
User

+

Python

Unique
```

Correct.

---

# Benefits

Now

User A

```
Python
```

Allowed

---

User B

```
Python
```

Allowed

---

User A

```
Python

Python
```

Rejected.

Perfect.

---

# Best Practices

* Scope uniqueness rules to the business requirement.
* Enforce uniqueness at two levels:

  * Application layer (service validation)
  * Database layer (unique compound index)
* Return meaningful error messages for duplicate resources.
* Avoid global uniqueness unless the business domain requires it (e.g., email addresses).

---

# Common Mistakes

❌

Global Duplicate Check

```python
get_by_name()
```

---

✔

Per User Duplicate

```python
get_by_name_and_user()
```

---

❌

Unique Skill Name

---

✔

Unique

```
User

+

Skill
```

---

# Authentication Progress

✅ Registration

✅ Login

✅ JWT

✅ OAuth2

✅ Current User

✅ Ownership Validation

✅ User-Specific Duplicate Validation

⬜ Refresh Token

⬜ Logout

⬜ RBAC

---

# Interview Questions

1. Why is global duplicate validation incorrect in a multi-user application?
2. What is a compound key?
3. Why create a compound index?
4. What does `$ne` do in MongoDB?
5. Why enforce uniqueness in both the application and the database?

---

# Summary

Today we upgraded SkillHub to support proper multi-user behavior.

We implemented:

* ✅ User-specific duplicate validation
* ✅ Repository method using `name + user_id`
* ✅ Update-safe duplicate checking
* ✅ Prepared the project for compound MongoDB indexes

This is the same design pattern used in many production SaaS applications.

---

# 🚀 Next Lesson (Module 5 — Part 9)

We'll implement **Refresh Tokens**.

You'll learn:

* Why Access Tokens expire
* Short-lived vs Long-lived tokens
* Refresh Token flow
* New `/auth/refresh` endpoint
* Rotating access tokens
* Secure token lifecycle management

This is the next major step toward a production-grade authentication system.
