# Backend Development with FastAPI + MongoDB

# **Module 3 — Part 4**

# **Production-Ready CRUD**

## **ObjectId Validation + Global Exception Handling + API Response Standardization**

> ⭐ **Today's lesson is one of the most important in backend development.**
>
> Our CRUD works...
>
> But it is **NOT production ready.**

Today we'll improve it the way experienced backend developers do.

---

# 1. Learning Objectives

After this lesson you will understand:

* Why production APIs never expose raw exceptions
* ObjectId validation
* Custom exception handling
* Global exception handlers
* Consistent API responses
* Clean architecture improvements

---

# 2. Current Problem

Suppose a client sends

```http
GET /skills/abc
```

Our current code

```python
ObjectId(skill_id)
```

tries converting

```text
abc
```

into

```text
ObjectId(...)
```

MongoDB says

```text
InvalidId
```

FastAPI returns

```text
500 Internal Server Error
```

Question:

Is this the server's mistake?

No.

The client gave an invalid ID.

So

```text
500 ❌

400 or 404 ✅
```

---

# 3. What Does a Professional API Return?

Bad

```json
{
    "detail":"Internal Server Error"
}
```

Good

```json
{
    "success": false,
    "message": "Invalid Skill ID"
}
```

Much cleaner.

---

# 4. Why Centralize Error Handling?

Imagine

Tomorrow we have

```text
Users API

Skills API

Projects API

Comments API

Bookmarks API

Likes API

Categories API
```

If every API contains

```python
try:
    ...
except:
    ...
```

Thousands of lines.

Instead

Professional applications

↓

One place handles all exceptions.

---

# 5. New Folder

Create

```text
app/

exceptions/

    handlers.py
```

---

# 6. Understanding ObjectId Validation

MongoDB already provides

```python
ObjectId.is_valid()
```

Example

```python
ObjectId.is_valid("6868ab...")
```

Returns

```python
True
```

Example

```python
ObjectId.is_valid("abc")
```

Returns

```python
False
```

This is much better than letting MongoDB throw an exception.

---

# 7. Create Utility Function

Instead of checking everywhere

Create

```text
app/

utils/

object_id.py
```

---

## Code

```python
from bson import ObjectId


def validate_object_id(id: str):

    if not ObjectId.is_valid(id):
        return None

    return ObjectId(id)
```

---

# Why Utility?

Without utility

Every API repeats

```python
if not ObjectId.is_valid():
```

Again...

Again...

Again...

Instead

```python
validate_object_id()
```

One line.

---

# 8. Update GET API

Old

```python
ObjectId(skill_id)
```

New

```python
from app.utils.object_id import validate_object_id

object_id = validate_object_id(skill_id)
```

Now

Check

```python
if object_id is None:

    raise HTTPException(
        status_code=400,
        detail="Invalid Skill ID"
    )
```

Much cleaner.

---

# 9. Update Query

Instead of

```python
ObjectId(skill_id)
```

Use

```python
object_id
```

Example

```python
skill = await database.skills.find_one(
    {
        "_id": object_id
    }
)
```

Simple.

---

# 10. Repeat Everywhere?

Question.

Should we also update

PUT

DELETE

Yes.

Anywhere we use

```python
ObjectId(...)
```

Replace it with

```python
validate_object_id()
```

Now

Every API becomes safe.

---

# 11. But We Still Have a Problem

Current API returns

404

Like this

```json
{
"detail":"Skill Not Found"
}
```

Another API returns

```json
{
"message":"Invalid Login"
}
```

Another

```json
{
"error":"Database Error"
}
```

Three APIs

Three formats.

Frontend developers hate this.

---

# 12. Standard Response Format

Professional APIs follow one format.

Success

```json
{
    "success": true,
    "message": "Skill Created",
    "data": {}
}
```

Failure

```json
{
    "success": false,
    "message": "Skill Not Found"
}
```

Frontend becomes much easier.

---

# 13. Global Exception Handler

Create

```text
app/

exceptions/

handlers.py
```

---

## Code

```python
from fastapi import Request

from fastapi.responses import JSONResponse

from fastapi import HTTPException
```

---

Add

```python
async def http_exception_handler(
    request: Request,
    exc: HTTPException
):

    return JSONResponse(

        status_code=exc.status_code,

        content={

            "success": False,

            "message": exc.detail

        }
    )
```

Let's understand.

---

# 14. Visual Flow

Old

```text
API

↓

HTTPException

↓

FastAPI

↓

detail
```

New

```text
API

↓

HTTPException

↓

Global Handler

↓

Custom JSON

↓

Frontend
```

Powerful.

---

# 15. Register Handler

Open

```text
main.py
```

Import

```python
from fastapi import HTTPException

from app.exceptions.handlers import (
    http_exception_handler
)
```

Register

```python
app.add_exception_handler(
    HTTPException,
    http_exception_handler
)
```

Done.

Every API now uses it automatically.

---

# 16. Test

Old Response

```json
{
"detail":"Skill Not Found"
}
```

New Response

```json
{
"success":false,
"message":"Skill Not Found"
}
```

No code changed inside

* Skills API
* Users API
* Login API

Amazing.

---

# 17. Another Improvement

Current POST

Returns

```json
{
"id":"..."
}
```

Professional APIs

Usually return

```json
{
"success":true,
"message":"Skill Created",
"data":{
"id":"..."
}
}
```

Much cleaner.

---

# 18. Reusable Success Response

Create

```text
utils/

responses.py
```

---

## Code

```python
def success_response(
    message: str,
    data=None
):

    return {

        "success": True,

        "message": message,

        "data": data
    }
```

---

Now

Instead of

```python
return {
...
}
```

Simply

```python
return success_response(
    "Skill Created",
    {
        "id": str(result.inserted_id)
    }
)
```

Every API now looks identical.

---

# 19. Final Architecture

```text
Client

↓

Skills API

↓

Utility

↓

Validation

↓

MongoDB

↓

Response Utility

↓

Global Exception Handler

↓

JSON
```

Notice how responsibilities are separated:

* Validation
* Database
* Response
* Exception

Each layer does one job.

---

# 20. Folder Structure

```text
app/

api/

core/

db/

schemas/

utils/
    serializer.py
    object_id.py
    responses.py

exceptions/
    handlers.py
```

The project is becoming more organized with every lesson.

---

# 21. API Testing

Test

```text
GET /skills/abc
```

Response

```json
{
"success":false,
"message":"Invalid Skill ID"
}
```

---

Test

```text
GET /skills/68688....
```

Not Found

```json
{
"success":false,
"message":"Skill Not Found"
}
```

---

Create

```text
POST /skills
```

Response

```json
{
"success":true,
"message":"Skill Created",
"data":{
"id":"6868..."
}
}
```

---

# 22. Common Errors

## Forgot

```python
app.add_exception_handler()
```

Handler never runs.

---

## Returning

```python
return None
```

Wrong.

Raise

```python
HTTPException
```

---

## Forgot ObjectId Validation

Leads to

```text
InvalidId
```

Again.

---

# 23. Interview Questions

1. Why validate ObjectId?
2. Why not expose raw exceptions?
3. What is Global Exception Handling?
4. Benefits of consistent API responses?
5. Why create utility functions?
6. Difference between 400 and 404?

---

# 24. Assignment

Update

* GET
* PUT
* DELETE

Use

```python
validate_object_id()
```

Then

Implement

```python
success_response()
```

Register

Global Exception Handler.

---

# 25. Mini Challenge

Extend the `success_response()` helper to include a timestamp:

```json
{
  "success": true,
  "message": "Skill Created",
  "data": {
    "id": "6868..."
  },
  "timestamp": "2026-07-09T10:30:45Z"
}
```

Use Python's `datetime` module to generate the current UTC timestamp in ISO 8601 format.

---

# 26. Best Practices

* Validate client input before querying the database.
* Use reusable utility functions instead of duplicating logic.
* Centralize exception handling in one place.
* Keep API response formats consistent across the application.
* Avoid exposing internal implementation details or raw exceptions to API consumers.

---

# 27. Summary

Today we transformed our CRUD APIs from "working" to "production-oriented" by adding:

* ✅ ObjectId validation
* ✅ Reusable ID validation utility
* ✅ Global HTTP exception handling
* ✅ Standardized success responses
* ✅ Cleaner project architecture

At this point, our Skills module has a solid foundation that we can reuse for every future collection in the project.

---

# ⚠️ Architectural Improvement Before We Continue

So far, we've written database queries directly inside the API routes:

```python
@router.post("/skills")
async def create_skill(...):
    await database.skills.insert_one(...)
```

This is acceptable for learning CRUD, but **it's not how large production systems are structured**.

As our project grows to include:

* Users
* Authentication
* Comments
* Bookmarks
* Reviews
* Search
* Admin features

the route files will become cluttered.

## Next Lesson (Industry-Level Architecture)

Before creating more collections, we'll refactor the Skills module into a clean layered architecture:

* **Repository Layer** (MongoDB operations)
* **Service Layer** (Business logic)
* **API Layer** (HTTP endpoints)

This will teach you the architecture used in many professional FastAPI projects and make it much easier to add authentication, authorization, and advanced features later in the course.
