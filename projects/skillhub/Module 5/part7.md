Excellent. This lesson takes your backend from **authentication** to **authorization**.

Many beginners think:

> "User logged in → secure."

That's **not true**.

A logged-in user should only access **their own resources** unless they have additional permissions (such as an admin role).

---

# Module 5 — Part 7

# Resource Ownership Authorization

---

# Learning Objectives

By the end of this lesson, you'll learn:

* Ownership Validation
* Resource Authorization
* Forbidden vs Unauthorized
* Protect Update API
* Protect Delete API
* Secure Read API

---

# Problem Statement

Suppose

Database

```text
User A

↓

Python Skill

id = S1
```

Another user

```text
User B
```

knows

```text
S1
```

If User B calls

```http
PUT /skills/S1
```

Current backend

↓

Updates Skill.

😱

Huge security issue.

---

# Desired Flow

```text
User B

↓

PUT /skills/S1

↓

JWT

↓

Current User

↓

Owner?

↓

No

↓

403 Forbidden
```

---

# Authentication vs Authorization

Authentication

```text
WHO ARE YOU?
```

Authorization

```text
CAN YOU DO THIS?
```

Today

We're implementing

Authorization.

---

# Step 1 — Repository

We'll reuse

Already existing

```python
get_by_id()
```

Good news

No repository changes needed.

---

# Step 2 — Service

Create helper

```python
async def validate_skill_owner(
    self,
    skill_id,
    current_user
):
```

---

Get Skill

```python
skill = await skill_repository.get_by_id(
    skill_id
)
```

---

Not Found

```python
if skill is None:

    raise HTTPException(

        status_code=404,

        detail="Skill Not Found"

    )
```

---

Check Owner

```python
if skill["user_id"] != current_user["_id"]:

    raise HTTPException(

        status_code=403,

        detail="You are not allowed to access this skill"

    )
```

Return

```python
return skill
```

---

Complete Helper

```python
async def validate_skill_owner(
    self,
    skill_id,
    current_user
):

    skill = await skill_repository.get_by_id(
        skill_id
    )

    if skill is None:

        raise HTTPException(

            status_code=404,

            detail="Skill Not Found"

        )

    if skill["user_id"] != current_user["_id"]:

        raise HTTPException(

            status_code=403,

            detail="You are not allowed to access this skill"

        )

    return skill
```

---

# Why 403?

Not

401.

401

Means

```text
Not Logged In
```

403

Means

```text
Logged In

BUT

No Permission
```

Very important.

---

# Step 3 — Update API

Current

```python
update_skill(
object_id,
skill
)
```

New

```python
update_skill(

object_id,

skill,

current_user

)
```

---

First

Validate

```python
await self.validate_skill_owner(

    object_id,

    current_user

)
```

Only then

Update.

---

Visual

```text
Request

↓

JWT

↓

Current User

↓

Owner?

↓

YES

↓

Update

↓

MongoDB
```

---

# Step 4 — Delete API

Same

```python
await self.validate_skill_owner(
    object_id,
    current_user
)
```

↓

Delete.

---

# Step 5 — Router

Update

```python
PUT
```

```python
@router.put("/{skill_id}")
async def update_skill(

    skill_id: str,

    skill: SkillUpdate,

    current_user=Depends(
        get_current_user
    )

):
```

---

Call

```python
await skill_service.update_skill(

    object_id,

    skill,

    current_user

)
```

---

Delete

Exactly same.

---

# Step 6 — Read Skill

Question

Should

```http
GET /skills/{id}
```

return

another user's skill?

Depends.

Business Requirement.

For

SkillHub

We'll protect it.

---

```python
skill = await skill_service.validate_skill_owner(

    object_id,

    current_user

)
```

Return

Skill.

Done.

---

# My Skills

Already secure.

```http
GET

/skills/me
```

No changes.

---

# Request Flow

```text
Client

↓

JWT

↓

Current User

↓

Skill Owner?

↓

YES

↓

Update/Delete
```

---

# Unauthorized Access

User A

```text
Python
```

User B

```http
DELETE /skills/python_id
```

↓

403

```json
{
"message":

"You are not allowed to access this skill"
}
```

Perfect.

---

# Security Layers

Layer 1

```text
JWT

↓

Current User
```

Layer 2

```text
Current User

↓

Skill Owner
```

Both

Must pass.

---

# Production Flow

```text
Authentication

↓

Authorization

↓

Business Logic

↓

Database
```

Never

```text
JWT

↓

Database
```

---

# Common Mistake

Only

```python
Depends(
get_current_user
)
```

Not enough.

Need

Ownership.

---

# Real Example

GitHub

Logged in?

↓

Yes.

Can edit

Someone else's repository?

↓

No.

Exactly same.

---

# Best Practices

* Separate authentication from authorization.
* Return **401 Unauthorized** when the user is not authenticated.
* Return **403 Forbidden** when the user is authenticated but lacks permission.
* Centralize ownership checks in reusable service methods.
* Never trust resource IDs alone; always verify ownership.

---

# Authentication Progress

✅ Registration

✅ Hash Password

✅ Login

✅ JWT

✅ OAuth2

✅ Current User

✅ Protected APIs

✅ Ownership Validation

⬜ Refresh Token

⬜ Logout

⬜ Roles

---

# Interview Questions

1. Authentication vs Authorization?
2. Difference between 401 and 403?
3. Why create `validate_skill_owner()`?
4. Should routers perform ownership checks?
5. Why centralize authorization logic in the Service Layer?

---

# Summary

Today we implemented one of the most important backend security patterns:

* ✅ Ownership validation
* ✅ Protected update
* ✅ Protected delete
* ✅ Protected read
* ✅ 403 Forbidden for unauthorized access

Your SkillHub application now ensures that authenticated users can only access **their own skills**.

---

# 🚀 Next Lesson (Module 5 — Part 8)

We'll improve the data model and implement **user-specific duplicate validation**.

Instead of this incorrect rule:

```text
Python ❌
Python ❌
```

we'll support:

```text
User A
Python ✅

User B
Python ✅

User A
Python ❌
```

using a compound uniqueness check (`user_id + skill_name`) and prepare the project for MongoDB compound indexes. This is the approach used in real multi-user applications.
