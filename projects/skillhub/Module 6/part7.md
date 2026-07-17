Excellent. Since we're following a production architecture, the next complete file is the repository.

> **Note:** I'm providing a complete repository implementation that supports the features we've been building (ownership checks and user-scoped duplicate validation). Replace your existing `skill_repository.py` with this version.

---

# Module 6 — Part 7

## Complete Updated File

**File**

```text
app/repositories/skill_repository.py
```

```python
from bson import ObjectId

from app.db.database import database


class SkillRepository:

    async def create(
        self,
        data: dict
    ):
        return await database.skills.insert_one(
            data
        )

    async def get_all(
        self
    ):
        return await database.skills.find().to_list(
            length=None
        )

    async def get_by_id(
        self,
        object_id: ObjectId
    ):
        return await database.skills.find_one(
            {
                "_id": object_id
            }
        )

    async def get_by_user(
        self,
        user_id: ObjectId
    ):
        return await database.skills.find(
            {
                "user_id": user_id
            }
        ).to_list(
            length=None
        )

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

    async def get_duplicate_for_update(
        self,
        skill_id: ObjectId,
        user_id: ObjectId,
        name: str
    ):
        return await database.skills.find_one(
            {
                "_id": {
                    "$ne": skill_id
                },
                "user_id": user_id,
                "name": name
            }
        )

    async def update(
        self,
        object_id: ObjectId,
        data: dict
    ):
        await database.skills.update_one(
            {
                "_id": object_id
            },
            {
                "$set": data
            }
        )

    async def delete(
        self,
        object_id: ObjectId
    ):
        await database.skills.delete_one(
            {
                "_id": object_id
            }
        )


skill_repository = SkillRepository()
```

---

# New Repository Methods

## Create

```python
create()
```

Adds a new skill.

---

## Get All

```python
get_all()
```

Returns all skills.

---

## Get By ID

```python
get_by_id()
```

Returns one skill.

---

## Get User Skills

```python
get_by_user()
```

Used by

```http
GET /skills/me
```

---

## Duplicate Validation

```python
get_by_name_and_user()
```

Checks

```
name

+

user_id
```

instead of only

```
name
```

---

## Update Duplicate Validation

```python
get_duplicate_for_update()
```

Ignores current document.

Example

```
Python

↓

Python

Same Record

↓

Allowed
```

But

```
Python

↓

Java → Python

Another Record

↓

Rejected
```

---

## Update

```python
update()
```

Uses

```python
$set
```

instead of replacing the whole document.

---

## Delete

Deletes one skill.

---

# Repository Responsibility

Repository should **only** communicate with MongoDB.

✔ CRUD

✔ Queries

✔ Filters

✔ Aggregations (later)

It should **never** contain:

* Business validation
* Authorization
* Password hashing
* JWT handling
* Role checks

Those belong in the Service layer.

---

# Best Practices

* Keep repositories free of business logic.
* Use `ObjectId` in repository methods.
* Use `$set` for updates.
* Create focused query methods instead of generic, complex methods.
* Let the service layer decide **when** to call repository methods.

---

# Summary

Today we completed the **Skill Repository**.

We now support:

* ✅ CRUD operations
* ✅ User-specific queries
* ✅ Duplicate validation
* ✅ Update-safe duplicate checking
* ✅ Proper repository separation

---

# 🚀 Next Lesson (One Complete File)

The next file will be the **complete updated**:

```text
app/services/skill_service.py
```

This will be the largest file so far and will include:

* User-scoped duplicate validation
* Ownership authorization
* Admin override
* `/skills/me`
* Secure create/update/delete logic
* Production-ready service layer built on the repository we completed today.
