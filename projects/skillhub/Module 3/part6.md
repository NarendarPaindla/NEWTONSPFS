Excellent. From this point onward, we'll start implementing features that almost every production API provides.

---

# Backend Development with FastAPI + MongoDB

# **Module 3 — Part 6**

# **Pagination, Filtering, Sorting & Searching (Industry Level APIs)**

> ⭐ This is one of the most frequently asked interview topics.
>
> Almost every backend application has list APIs like:
>
> * Amazon Products
> * Netflix Movies
> * GitHub Repositories
> * LinkedIn Jobs
> * Instagram Posts
>
> None of these applications return **all data** at once.

Today you'll learn how professional APIs solve this problem.

---

# 1. Learning Objectives

By the end of this lesson, you'll understand:

* Why Pagination is necessary
* Query Parameters
* MongoDB `skip()`
* MongoDB `limit()`
* MongoDB `sort()`
* MongoDB filtering
* MongoDB regex search
* Dynamic query building
* Clean API design

---

# 2. Real World Problem

Suppose SkillHub has

```text
10 Skills
```

Returning all skills is okay.

Now imagine

```text
10,000 Skills
```

or

```text
5,00,000 Skills
```

Should we return everything?

No.

Imagine Netflix returning every movie ever made when you open the homepage.

Impossible.

---

# 3. Solution

Instead of returning everything

Return only

```text
Page 1

↓

10 Records
```

Next request

```text
Page 2

↓

Next 10 Records
```

This is called **Pagination**.

---

# 4. Visual Example

Database

```text
1 Python
2 Java
3 FastAPI
4 MongoDB
5 Docker
6 Redis
7 React
8 Angular
9 Node
10 SQL
11 Git
12 Linux
```

Client requests

```http
GET /skills?page=1&limit=5
```

Returns

```text
Python
Java
FastAPI
MongoDB
Docker
```

Next request

```http
GET /skills?page=2&limit=5
```

Returns

```text
Redis
React
Angular
Node
SQL
```

---

# 5. Understanding Query Parameters

URL

```http
GET /skills?page=2&limit=5
```

Question

Where do

```text
page

limit
```

come from?

They are **Query Parameters**.

General Syntax

```text
?

key=value

&

key=value
```

Example

```http
GET /skills?page=2&limit=10
```

FastAPI automatically reads them.

---

# 6. Update API

```python
from fastapi import Query
from typing import Annotated
```

```python
@router.get(
    "/",
    response_model=list[SkillResponse]
)
async def get_skills(

    page: Annotated[int, Query(ge=1)] = 1,

    limit: Annotated[int, Query(ge=1, le=100)] = 10
):
```

---

# 7. Why Use `Query()`?

Suppose user sends

```http
?page=-10
```

Should this be allowed?

No.

Validation

```python
Query(ge=1)
```

means

Greater than or Equal to 1.

Similarly

```python
Query(le=100)
```

means

Maximum 100 records.

This prevents clients from requesting huge amounts of data in one call.

---

# 8. Pagination Formula

This is one of the most important formulas in backend development.

```python
skip = (page - 1) * limit
```

Let's understand it.

---

## Page 1

```text
page = 1
limit = 10

skip = (1-1)*10

skip = 0
```

MongoDB skips nothing.

---

## Page 2

```text
page = 2

skip=(2-1)*10

skip=10
```

Skip first ten records.

---

## Page 3

```text
skip=(3-1)*10

skip=20
```

Skip first twenty records.

Very simple.

Remember this formula.

---

# 9. Update Repository

Open

```text
repositories/

skill_repository.py
```

Replace

```python
get_all()
```

with

```python
async def get_all(
    self,
    skip: int,
    limit: int
):
    return await (
        database.skills
        .find()
        .skip(skip)
        .limit(limit)
        .to_list(length=limit)
    )
```

---

# 10. Understanding the Flow

Suppose

```text
page=3

limit=5
```

Then

```text
skip=(3-1)*5

skip=10
```

MongoDB executes

```python
.find()

↓

.skip(10)

↓

.limit(5)
```

Returns

Records

11

12

13

14

15

Exactly what we need.

---

# 11. Update Service

```python
async def get_all_skills(
    self,
    page,
    limit
):

    skip=(page-1)*limit

    return await skill_repository.get_all(
        skip,
        limit
    )
```

Notice

The repository doesn't know about pages.

It only understands

```text
Skip

Limit
```

The business logic (pagination formula) belongs in the **Service Layer**.

---

# 12. Update API

```python
skills = await skill_service.get_all_skills(
    page,
    limit
)

return skill_serializer_list(skills)
```

That's it.

---

# 13. Testing Pagination

Database contains

30 Skills.

Request

```http
GET /skills?page=1&limit=5
```

Returns

```text
1

2

3

4

5
```

Request

```http
GET /skills?page=2&limit=5
```

Returns

```text
6

7

8

9

10
```

Perfect.

---

# 14. Filtering

Now users want

Only Backend Skills.

Example

```http
GET /skills?category=Backend
```

How?

MongoDB filter

```python
.find(
{
"category":"Backend"
}
)
```

Only Backend documents.

---

# 15. Dynamic Query

Suppose user doesn't provide category.

Should we write

```python
if

else

if

else
```

No.

Professional developers build queries dynamically.

```python
query={}
```

If category exists

```python
query["category"]=category
```

Otherwise

```python
query
```

remains empty.

MongoDB

```python
.find({})
```

means

Return everything.

Elegant and scalable.

---

# 16. Repository

```python
async def get_all(
    self,
    query,
    skip,
    limit
):

    return await (
        database.skills
        .find(query)
        .skip(skip)
        .limit(limit)
        .to_list(length=limit)
    )
```

---

# 17. Service

```python
query={}
```

If

```python
category
```

exists

```python
query["category"]=category
```

Return

```python
repository.get_all(
query,
skip,
limit
)
```

---

# 18. Search

User wants

```text
Python
```

Should they type

```text
Python
```

Exactly?

No.

Searching

```text
py

↓

Python
```

is better.

MongoDB provides

```python
regex
```

Example

```python
query["name"]={
"$regex":search,

"$options":"i"
}
```

The `"i"` option makes the search **case-insensitive**.

Search

```text
py
```

matches

```text
Python

PYTHON

python

PyThOn
```

---

# 19. Sorting

Users want

```text
A-Z
```

or

```text
Z-A
```

MongoDB

```python
.sort(
"name",
1
)
```

Ascending.

Descending

```python
.sort(
"name",
-1
)
```

---

# 20. Final API

```http
GET /skills?

page=2

&limit=5

&category=Backend

&search=python

&sort=name
```

One API.

Many possibilities.

---

# 21. Visual Flow

```text
Client

↓

Query Parameters

↓

Router

↓

Service

↓

Build Query

↓

Repository

↓

MongoDB

↓

Filtered Data

↓

Serializer

↓

JSON
```

This is how large applications process list endpoints.

---

# 22. API Examples

## Pagination

```http
GET /skills?page=2&limit=10
```

---

## Filter

```http
GET /skills?category=Backend
```

---

## Search

```http
GET /skills?search=python
```

---

## Combined

```http
GET /skills?

page=2

&limit=5

&category=Backend

&search=python
```

This is a realistic production endpoint.

---

# 23. Current Problem

Our response currently looks like this:

```json
[
...
]
```

Question.

How does frontend know

* Total Records?
* Total Pages?
* Current Page?
* Has Next Page?

It doesn't.

This is a limitation.

---

# 24. Common Errors

## Wrong Skip Formula

```python
skip=page*limit
```

Wrong.

Correct

```python
skip=(page-1)*limit
```

---

## Missing Query Validation

Allowing

```http
?page=-100
```

Bad API.

Use `Query(ge=1)`.

---

## Regex Without Case-Insensitive Option

```python
"$regex":search
```

This makes searches case-sensitive.

Prefer:

```python
"$options":"i"
```

unless your requirements are different.

---

# 25. Interview Questions

1. Why do we use pagination?
2. Difference between `skip()` and `limit()`?
3. Explain the pagination formula.
4. What are query parameters?
5. What is MongoDB regex search?
6. How do you implement filtering?
7. Why build queries dynamically?

---

# 26. Assignment

Implement:

* Pagination
* Filtering
* Search

Test:

```http
?page=3&limit=5
```

```http
?category=Backend
```

```http
?search=python
```

Then combine all three in a single request.

---

# 27. Mini Challenge

Add support for sorting by experience.

Example:

```http
GET /skills?sort=experience&order=desc
```

Map:

* `order=asc` → `1`
* `order=desc` → `-1`

Only allow sorting by approved fields (for example, `name`, `category`, `experience`) to avoid invalid client input.

---

# 28. Best Practices

* Always paginate collection endpoints.
* Validate query parameters.
* Build MongoDB queries dynamically instead of using many `if/else` blocks.
* Use case-insensitive search where appropriate.
* Keep pagination calculations in the Service Layer and database operations in the Repository Layer.

---

# 29. Summary

Today we enhanced our Skills API with production-style list features:

* ✅ Pagination
* ✅ Query parameter validation
* ✅ Dynamic filtering
* ✅ Case-insensitive search
* ✅ Sorting
* ✅ Clean layered architecture

Our API is now much closer to what you'll see in real-world backend systems.

---

# 🚀 Next Lesson (Industry-Level Pagination Response)

Our list endpoint still returns only an array of skills.

In the next lesson, we'll make it fully production-ready by returning pagination metadata like:

```json
{
  "success": true,
  "message": "Skills fetched successfully",
  "data": [
    ...
  ],
  "pagination": {
    "page": 2,
    "limit": 5,
    "total_records": 47,
    "total_pages": 10,
    "has_next": true,
    "has_previous": true
  }
}
```

We'll also learn how to use MongoDB's `count_documents()` efficiently so the frontend can build proper page navigation. This is the style of response you'll encounter in many professional REST APIs.
