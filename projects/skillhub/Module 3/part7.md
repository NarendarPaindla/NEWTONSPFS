# Backend Development with FastAPI + MongoDB

# **Module 3 — Part 7**

# **Production-Level Pagination Response + Total Count + API Metadata**

> ⭐ This lesson teaches how professional APIs return paginated data.
>
> Up to now, our API returns only a list:
>
> ```json
> [
>   {...},
>   {...}
> ]
> ```
>
> But real-world APIs return much more information.

---

# 1. Learning Objectives

By the end of this lesson, you will learn:

* Why pagination metadata is important
* MongoDB `count_documents()`
* Total Records
* Total Pages
* Has Next Page
* Has Previous Page
* Building reusable paginated responses
* Production API response format

---

# 2. Current Problem

Suppose the frontend calls:

```http
GET /skills?page=3&limit=10
```

Current Response

```json
[
    {
        "id":"..."
    },
    {
        "id":"..."
    }
]
```

Question

How does frontend know

* Total records?
* Total pages?
* Is there a next page?
* Is page 3 the last page?

Answer

It doesn't.

---

# 3. Real World APIs

Most production APIs return

```json
{
    "success": true,
    "message": "Skills fetched successfully",
    "data": [],
    "pagination": {}
}
```

Notice

The list is only one part of the response.

---

# 4. Desired Response

```json
{
    "success": true,
    "message": "Skills fetched successfully",

    "data": [
        {
            "id":"1",
            "name":"Python"
        }
    ],

    "pagination":{

        "page":1,

        "limit":10,

        "total_records":250,

        "total_pages":25,

        "has_next":true,

        "has_previous":false
    }
}
```

This is much more useful.

---

# 5. How Do We Know Total Records?

MongoDB provides

```python
count_documents()
```

Example

```python
await database.skills.count_documents({})
```

Returns

```text
250
```

Even if we request

```text
10 Records
```

MongoDB still knows

There are

```text
250
```

records.

---

# 6. Repository Update

Open

```text
repositories/

skill_repository.py
```

Add

```python
async def count(
    self,
    query
):
    return await database.skills.count_documents(
        query
    )
```

Simple.

One responsibility.

---

# 7. Service Layer

Current

```text
Repository

↓

Skills
```

Now

Need

```text
Repository

↓

Skills

+

Total Count
```

---

Update Service

```python
skills = await skill_repository.get_all(
    query,
    skip,
    limit
)

total_records = await skill_repository.count(
    query
)
```

Now

We have

```text
Skills

+

Total Records
```

---

# 8. Total Pages Formula

Suppose

```text
250 Records

Limit =10
```

Question

How many pages?

Formula

```python
import math

total_pages = math.ceil(
    total_records / limit
)
```

---

## Example

250

/

10

=

25

Perfect.

---

Another

243

/

10

=

24.3

Should backend return

24?

No.

Need

25 Pages

Because last page contains

3 records.

Hence

```python
math.ceil()
```

---

# 9. Has Next Page

Formula

```python
page < total_pages
```

Example

Page

```text
2
```

Total

```text
25
```

Result

```python
True
```

---

Last Page

```text
25
```

Total

```text
25
```

Result

```python
False
```

---

# 10. Has Previous

Very easy.

```python
page > 1
```

Page

```text
1
```

↓

False

---

Page

```text
5
```

↓

True

---

# 11. Service Returns

Instead of returning

```python
skills
```

Return

```python
{
    "skills":skills,

    "total_records":total_records,

    "page":page,

    "limit":limit,

    "total_pages":total_pages,

    "has_next":page<total_pages,

    "has_previous":page>1
}
```

Notice

Service calculates business logic.

Repository only reads database.

Perfect architecture.

---

# 12. Serializer

Current

```text
Serializer

↓

List
```

Need

```text
List

+

Pagination
```

---

Create

```text
utils/

pagination.py
```

---

# Code

```python
def paginated_response(

    message,

    data,

    page,

    limit,

    total_records,

    total_pages,

    has_next,

    has_previous
):

    return {

        "success":True,

        "message":message,

        "data":data,

        "pagination":{

            "page":page,

            "limit":limit,

            "total_records":total_records,

            "total_pages":total_pages,

            "has_next":has_next,

            "has_previous":has_previous
        }
    }
```

Beautiful.

Reusable.

---

# 13. Update API

Instead of

```python
return skill_serializer_list(
skills
)
```

Now

```python
return paginated_response(

    message="Skills fetched successfully",

    data=skill_serializer_list(
        result["skills"]
    ),

    page=result["page"],

    limit=result["limit"],

    total_records=result["total_records"],

    total_pages=result["total_pages"],

    has_next=result["has_next"],

    has_previous=result["has_previous"]
)
```

Professional.

---

# 14. Visual Flow

```text
Client

↓

Router

↓

Service

↓

Repository

↓

MongoDB

↓

Skills

+

Count

↓

Service

↓

Pagination Calculation

↓

Response Utility

↓

JSON
```

Notice

Every layer has exactly one responsibility.

---

# 15. API Example

Request

```http
GET /skills?page=2&limit=5
```

Response

```json
{
    "success":true,

    "message":"Skills fetched successfully",

    "data":[

        {
            "id":"1",
            "name":"FastAPI"
        },

        {
            "id":"2",
            "name":"MongoDB"
        }

    ],

    "pagination":{

        "page":2,

        "limit":5,

        "total_records":28,

        "total_pages":6,

        "has_next":true,

        "has_previous":true
    }
}
```

Exactly what frontend developers need.

---

# 16. Frontend Benefits

Frontend can now easily build:

```text
< Previous

1

2

3

4

5

Next >
```

Because backend provides

* Total Pages
* Current Page
* Next
* Previous

No guesswork.

---

# 17. Why Count Separately?

Many students ask

> We already have 5 records.

Why count again?

Suppose

Database

```text
10,000 Records
```

Current page

```text
5 Records
```

Without counting

Backend never knows

There are

```text
10,000
```

records.

---

# 18. Performance Note

`count_documents()` performs an additional database operation.

For extremely large datasets, you may need more advanced strategies such as cached counts, estimated counts, or cursor-based pagination. For most business applications, `count_documents()` is an appropriate and accurate choice.

---

# 19. Folder Structure

```text
app/

api/

services/

repositories/

utils/
    serializer.py
    responses.py
    pagination.py
```

The project keeps growing in a clean, maintainable way.

---

# 20. Common Errors

## Integer Division

Wrong

```python
250//10
```

Fails

When

243 Records

Need

25 Pages

Use

```python
math.ceil()
```

---

## Forget Count

Then

Frontend never knows

Total Pages.

---

## Pagination Formula

Wrong

```python
page<=total_pages
```

for `has_next`.

Correct

```python
page<total_pages
```

---

# 21. Interview Questions

1. Why is pagination metadata important?
2. Difference between `count_documents()` and `find()`?
3. Why use `math.ceil()`?
4. How is `has_next` calculated?
5. Why does the Service Layer calculate pagination instead of the Repository?
6. What are the advantages of returning pagination metadata?

---

# 22. Assignment

Update your list endpoint to return:

* Total Records
* Total Pages
* Current Page
* Has Next
* Has Previous

Test with:

```http
?page=1&limit=5
```

```http
?page=2&limit=5
```

```http
?page=100&limit=5
```

Observe how the pagination metadata changes.

---

# 23. Mini Challenge

Extend the pagination response to include:

```json
"pagination": {
    "page": 2,
    "limit": 5,
    "total_records": 28,
    "total_pages": 6,
    "current_count": 5,
    "has_next": true,
    "has_previous": true
}
```

Where `current_count` is the number of items actually returned in the current page (`len(data)`).

---

# 24. Best Practices

* Always return pagination metadata for collection endpoints.
* Keep pagination calculations in the Service Layer.
* Use reusable response helper functions.
* Validate page and limit values.
* Avoid returning excessively large result sets.

---

# 25. Summary

Today we upgraded our list endpoint to a **production-style paginated API**.

We added:

* ✅ `count_documents()`
* ✅ Total Records
* ✅ Total Pages
* ✅ Current Page
* ✅ Has Next
* ✅ Has Previous
* ✅ Reusable pagination response helper

Our `GET /skills` endpoint now resembles the APIs used in many real-world applications.

---

# 🚀 Next Lesson

So far, all Skill operations are public.

In the next lesson, we'll begin **expanding the project** by introducing our second major collection:

# **Users**

We'll build:

* User document design
* User schemas
* User CRUD
* Relationships between Users and Skills
* Preparing the project for authentication

This is the first step toward implementing **JWT Authentication**, where every skill will belong to a specific user instead of existing independently.
