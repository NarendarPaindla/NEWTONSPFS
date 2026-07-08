# Backend Development with FastAPI + MongoDB

# **Module 3 — Part 3**

# **Update (PUT) & Delete (DELETE) APIs - Complete CRUD**

> 🎯 **Today's Goal**
>
> By the end of this lesson, our **SkillHub API** will support **Complete CRUD**:
>
> ✅ Create
> ✅ Read All
> ✅ Read One
> ✅ Update
> ✅ Delete

---

# 1. Learning Objectives

After this lesson, you will learn:

* How to update a document in MongoDB
* How to delete a document
* `update_one()`
* `delete_one()`
* `$set` operator
* `matched_count`
* `modified_count`
* `deleted_count`
* Proper HTTP Status Codes
* Complete CRUD Workflow

---

# 2. Before We Write Code

Let's imagine our database contains this document.

```json
{
    "_id": ObjectId("68682abc"),
    "name": "Python",
    "category": "Backend",
    "level": "Intermediate",
    "experience": 1
}
```

After one year...

The user becomes more experienced.

Now the document should become

```json
{
    "_id": ObjectId("68682abc"),
    "name": "Python",
    "category": "Backend",
    "level": "Advanced",
    "experience": 2
}
```

We don't create another document.

We **update** the existing one.

---

# 3. CRUD Review

```text
CREATE

↓

READ

↓

UPDATE

↓

DELETE
```

Every backend project performs these four operations repeatedly.

---

# 4. MongoDB Update

MongoDB provides

```python
update_one()
```

General syntax

```python
collection.update_one(
    filter,
    update
)
```

Example

```python
await database.skills.update_one(
    {"_id": ObjectId(skill_id)},
    {
        "$set": {
            "level": "Advanced"
        }
    }
)
```

---

# 5. Why `$set`?

Many beginners ask:

> Why can't we write

```python
{
    "level": "Advanced"
}
```

Good question.

Without `$set`

MongoDB interprets it as an update document with no update operator and raises an error.

With

```python
"$set"
```

MongoDB understands

```text
Update only these fields.

Everything else remains unchanged.
```

---

# 6. Visual Example

Current Document

```json
{
"name":"Python",
"category":"Backend",
"level":"Intermediate",
"experience":1
}
```

Update

```python
"$set":{
"experience":2
}
```

Result

```json
{
"name":"Python",
"category":"Backend",
"level":"Intermediate",
"experience":2
}
```

Notice

Only one field changed.

Everything else stayed the same.

---

# 7. Create Update Schema

Open

```text
schemas/skill_schema.py
```

Add

```python
class SkillUpdate(BaseModel):
    name: str = Field(..., min_length=2)
    category: str
    level: str
    experience: int = Field(..., ge=0)
```

At this stage, we require all fields during an update (a full update). Later, we'll learn **partial updates** using `PATCH`.

---

# 8. Import

```python
from app.schemas.skill_schema import (
    SkillCreate,
    SkillUpdate,
    SkillResponse
)
```

---

# 9. Update API

```python
@router.put(
    "/{skill_id}",
    response_model=SkillResponse
)
async def update_skill(
    skill_id: str,
    skill: SkillUpdate
):
```

Let's continue.

---

# 10. Update Logic

```python
result = await database.skills.update_one(
    {
        "_id": ObjectId(skill_id)
    },
    {
        "$set": skill.model_dump()
    }
)
```

Let's understand.

`model_dump()`

returns

```python
{
"name":"Python",
"category":"Backend",
"level":"Advanced",
"experience":2
}
```

Exactly what MongoDB needs.

---

# 11. Understanding `update_one()` Result

MongoDB returns an object.

Inside it

```python
result.matched_count

result.modified_count
```

These two properties are very important.

---

# 12. `matched_count`

Suppose

User requests

```text
PUT /skills/123
```

But

ID doesn't exist.

MongoDB

```text
Search

↓

Nothing Found
```

Result

```python
matched_count = 0
```

---

If found

```python
matched_count = 1
```

---

# 13. `modified_count`

Imagine current document

```text
Experience = 2
```

User again sends

```text
Experience = 2
```

Nothing changes.

Result

```python
modified_count = 0
```

If

Old

```text
2
```

New

```text
3
```

Then

```python
modified_count = 1
```

---

# 14. Complete Update API

```python
@router.put(
    "/{skill_id}",
    response_model=SkillResponse
)
async def update_skill(
    skill_id: str,
    skill: SkillUpdate
):

    result = await database.skills.update_one(
        {
            "_id": ObjectId(skill_id)
        },
        {
            "$set": skill.model_dump()
        }
    )

    if result.matched_count == 0:
        raise HTTPException(
            status_code=404,
            detail="Skill Not Found"
        )

    updated_skill = await database.skills.find_one(
        {
            "_id": ObjectId(skill_id)
        }
    )

    return skill_serializer(updated_skill)
```

---

# 15. Why Read Again?

Many students ask

> We already updated.

Why

```python
find_one()
```

again?

Because

`update_one()`

returns

```text
Update Result
```

NOT

```text
Updated Document
```

If you want the updated document,

you need another query.

---

# 16. Testing Update

Swagger

```text
PUT /skills/{id}
```

Body

```json
{
"name":"Python",
"category":"Backend",
"level":"Advanced",
"experience":4
}
```

Response

```json
{
"id":"...",
"name":"Python",
"category":"Backend",
"level":"Advanced",
"experience":4
}
```

---

# 17. Delete API

Now

User wants to remove

Docker skill.

MongoDB

```python
delete_one()
```

---

# 18. Syntax

```python
await database.skills.delete_one(
    {
        "_id": ObjectId(skill_id)
    }
)
```

---

# 19. Delete Result

MongoDB returns

```python
result.deleted_count
```

If deleted

```python
1
```

If not found

```python
0
```

---

# 20. Complete Delete API

```python
@router.delete(
    "/{skill_id}",
    status_code=status.HTTP_204_NO_CONTENT
)
async def delete_skill(skill_id: str):

    result = await database.skills.delete_one(
        {
            "_id": ObjectId(skill_id)
        }
    )

    if result.deleted_count == 0:
        raise HTTPException(
            status_code=404,
            detail="Skill Not Found"
        )
```

---

# 21. Why 204?

Delete successful.

Should backend return

```json
{
"message":"Deleted"
}
```

No.

Industry standard

```text
204 No Content
```

Meaning

```text
Successfully deleted.

Nothing to return.
```

---

# 22. Complete CRUD Flow

```text
POST /skills
        │
        ▼
MongoDB Insert

        │
        ▼
GET /skills

        │
        ▼
MongoDB Read

        │
        ▼
PUT /skills/{id}

        │
        ▼
MongoDB Update

        │
        ▼
DELETE /skills/{id}

        │
        ▼
MongoDB Delete
```

---

# 23. API Testing

## Create

```http
POST /skills
```

↓

Returns

```text
201 Created
```

---

## Read All

```http
GET /skills
```

↓

Returns

```text
200 OK
```

---

## Read One

```http
GET /skills/{id}
```

↓

Returns

```text
200 OK
```

or

```text
404 Not Found
```

---

## Update

```http
PUT /skills/{id}
```

↓

Returns

```text
200 OK
```

or

```text
404 Not Found
```

---

## Delete

```http
DELETE /skills/{id}
```

↓

Returns

```text
204 No Content
```

or

```text
404 Not Found
```

---

# 24. Common Errors

## Invalid ObjectId

```text
InvalidId
```

We'll improve this in the next lesson by validating IDs before querying MongoDB.

---

## Forgetting `$set`

```python
update_one(filter,data)
```

Wrong.

Use

```python
{
"$set":data
}
```

---

## Returning UpdateResult

Wrong

```python
return result
```

Correct

```python
return updated document
```

---

## Returning Body with 204

Avoid returning a JSON body with a `204 No Content` response. Either return nothing or choose a different status code if you want to send a response body.

---

# 25. Interview Questions

1. Difference between PUT and PATCH?
2. Why use `$set`?
3. What is `matched_count`?
4. What is `modified_count`?
5. What is `deleted_count`?
6. Why use `204 No Content`?
7. Why query again after `update_one()`?

---

# 26. Assignment

Implement:

* PUT
* DELETE

Then test

* Update existing skill
* Update invalid ID
* Delete existing skill
* Delete same skill again

Observe

* matched_count
* modified_count
* deleted_count

---

# 27. Mini Challenge

Add a new field:

```python
last_updated: datetime
```

Whenever a skill is updated, automatically set:

```python
last_updated = datetime.utcnow()
```

(We'll later refine this using timezone-aware timestamps.)

---

# 28. Best Practices

* Use `PUT` for full resource updates and `PATCH` for partial updates.
* Always check `matched_count` before assuming an update succeeded.
* Return the updated resource after a successful update.
* Return `204 No Content` for successful deletes without a response body.
* Validate resource existence before reporting success.

---

# 29. Summary

Today we completed the **CRUD** implementation for the `skills` collection:

* ✅ Create (`POST`)
* ✅ Read All (`GET`)
* ✅ Read One (`GET /{id}`)
* ✅ Update (`PUT`)
* ✅ Delete (`DELETE`)

You also learned:

* `update_one()`
* `delete_one()`
* `$set`
* `matched_count`
* `modified_count`
* `deleted_count`
* Proper HTTP status codes

At this stage, SkillHub has its first complete production-style CRUD module.

---

# 🚀 Next Lesson (Production-Level CRUD)

Our current API still has some weaknesses:

```text
GET /skills/abc
```

produces an `InvalidId` exception instead of a clean client error.

In the next lesson, we'll improve the API by adding:

* Request validation for MongoDB `ObjectId`
* Global exception handling
* Custom error responses
* Better API response consistency
* Reusable helper functions for ID validation

This is where our CRUD APIs start looking like those in professional production systems.
