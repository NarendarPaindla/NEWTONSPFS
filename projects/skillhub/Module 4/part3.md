Excellent. From this point onward, we move beyond basic CRUD and enter **Advanced MongoDB**.

This module is one of the most important in the entire course because companies expect backend developers to know how to retrieve data from multiple collections efficiently.

---

# Module 4 — Part 3

# MongoDB Aggregation Framework

# Lesson 1 — Introduction to Aggregation + `$match` + `$project`

> ⭐ Before learning `$lookup`, you must first understand **Aggregation Pipelines**.

Think of aggregation as MongoDB's way of processing data step by step.

---

# 1. Learning Objectives

By the end of this lesson, you will understand:

* What Aggregation is
* Why Aggregation is needed
* Aggregation Pipeline
* Pipeline Stages
* `$match`
* `$project`
* Building your first aggregation API

---

# 2. The Problem

Suppose your Skills collection contains:

```json
{
    "name":"Python",
    "category":"Backend",
    "level":"Advanced",
    "experience":5
}
```

Client wants

```text
Only Backend Skills
```

Easy.

We already know

```python
find(
{
"category":"Backend"
}
)
```

But tomorrow client asks

```text
Return only

Skill Name

Experience
```

Can `find()` do everything?

Sometimes.

But what if tomorrow they ask

* Join Users
* Calculate Average Experience
* Count Skills
* Group Categories

Now `find()` becomes limited.

MongoDB created

# Aggregation Framework

---

# 3. SQL Analogy

SQL

```sql
SELECT
name,
experience
FROM skills
WHERE category='Backend'
```

MongoDB

```python
aggregate([
    ...
])
```

Aggregation is MongoDB's equivalent of advanced SQL queries.

---

# 4. Aggregation Pipeline

Imagine a factory.

Raw Material

↓

Machine 1

↓

Machine 2

↓

Machine 3

↓

Finished Product

Aggregation works exactly like that.

```text
Documents

      │

      ▼

Stage 1

      │

      ▼

Stage 2

      │

      ▼

Stage 3

      │

      ▼

Result
```

Every stage transforms the data.

---

# 5. Syntax

```python
collection.aggregate(

[
    stage1,

    stage2,

    stage3
]
)
```

Notice

Aggregation receives

A

```python
List
```

Every item is one stage.

---

# 6. Our Sample Database

Skills

```json
{
"name":"Python",

"category":"Backend",

"level":"Advanced",

"experience":5
}
```

```json
{
"name":"React",

"category":"Frontend",

"level":"Intermediate",

"experience":2
}
```

```json
{
"name":"MongoDB",

"category":"Database",

"level":"Advanced",

"experience":4
}
```

---

# 7. Stage 1 — `$match`

Equivalent of SQL

```sql
WHERE
```

Example

```python
{
"$match":
{
"category":"Backend"
}
}
```

Returns

Only

```text
Python
```

---

# 8. Visual

Input

```text
Python

React

MongoDB
```

↓

```python
$match
```

↓

Output

```text
Python
```

Very simple.

---

# 9. Repository

Create a new method in

```text
repositories/

skill_repository.py
```

```python
async def get_backend_skills(
    self
):

    cursor = database.skills.aggregate(

        [

            {

                "$match":{

                    "category":"Backend"

                }

            }

        ]

    )

    return await cursor.to_list(
        length=None
    )
```

Notice

Aggregation returns

```python
Cursor
```

Just like

```python
find()
```

Need

```python
to_list()
```

---

# 10. Service

```python
async def get_backend_skills(
    self
):

    return await skill_repository.get_backend_skills()
```

Simple.

---

# 11. API

```python
@router.get(
    "/category/backend"
)
async def backend_skills():

    skills = await skill_service.get_backend_skills()

    return success_response(

        "Backend Skills",

        skill_serializer_list(skills)

    )
```

---

# 12. Test

Swagger

```http
GET

/skills/category/backend
```

Response

```json
[
Python
]
```

Only Backend Skills.

---

# 13. Why Use `$match` Instead of `find()`?

Good question.

Today

Both work.

Tomorrow

Pipeline

```text
Match

↓

Project

↓

Lookup

↓

Group
```

Now

Need Aggregation.

So we learn Aggregation from the beginning.

---

# 14. Stage 2 — `$project`

Question

Suppose frontend only needs

```text
Name

Experience
```

Should backend send

```text
Category

Level

User ID
```

No.

Waste of bandwidth.

---

# 15. Example

Current Document

```json
{
"name":"Python",

"category":"Backend",

"level":"Advanced",

"experience":5
}
```

Need

```json
{
"name":"Python",

"experience":5
}
```

Aggregation

```python
{

"$project":{

"name":1,

"experience":1

}

}
```

---

# 16. What Does `1` Mean?

Include.

```python
"name":1
```

↓

Return

Name.

---

`0`

Means

Exclude.

---

# 17. Important Rule

MongoDB automatically returns

```text
_id
```

Unless

You explicitly remove it.

---

Example

```python
{

"$project":{

"_id":0,

"name":1,

"experience":1

}

}
```

Result

```json
{
"name":"Python",

"experience":5
}
```

No `_id`.

---

# 18. Combining Stages

Pipeline

```python
[

{

"$match":{

"category":"Backend"

}

},

{

"$project":{

"_id":0,

"name":1,

"experience":1

}

}

]
```

Step 1

↓

Backend Only

Step 2

↓

Only

```text
Name

Experience
```

---

# 19. Visual Pipeline

```text
All Skills

        │

        ▼

$match

        │

        ▼

Backend Skills

        │

        ▼

$project

        │

        ▼

Name

Experience
```

Every stage transforms the previous result.

---

# 20. Repository Update

```python
async def backend_skill_summary(
    self
):

    cursor = database.skills.aggregate(

        [

            {

                "$match":{

                    "category":"Backend"

                }

            },

            {

                "$project":{

                    "_id":0,

                    "name":1,

                    "experience":1

                }

            }

        ]

    )

    return await cursor.to_list(
        length=None
    )
```

---

# 21. API

```python
@router.get(
    "/category/backend/summary"
)
async def backend_summary():

    data = await skill_service.backend_summary()

    return success_response(

        "Backend Summary",

        data

    )
```

---

# 22. Response

```json
[
{
"name":"Python",

"experience":5
},

{
"name":"FastAPI",

"experience":3
}
]
```

Notice

No

```text
_id

Category

Level

User ID
```

Only requested fields.

---

# 23. Difference

## find()

```python
find()
```

Simple retrieval.

---

## aggregate()

```python
aggregate()
```

Multi-stage processing.

---

# 24. Common Errors

## Forget

```python
to_list()
```

Returns Cursor.

---

## Wrong

```python
"name":"1"
```

Must be

```python
"name":1
```

---

## Forget

```python
"_id":0
```

MongoDB returns `_id`.

---

# 25. Interview Questions

1. What is MongoDB Aggregation?
2. What is an Aggregation Pipeline?
3. Difference between `find()` and `aggregate()`?
4. What does `$match` do?
5. What does `$project` do?
6. Why is `_id` returned automatically?

---

# 26. Assignment

Create APIs

```http
GET

/skills/category/backend
```

---

```http
GET

/skills/category/frontend
```

---

```http
GET

/skills/category/database
```

Then

Create

Summary API

Using

```python
$project
```

Return only

```text
Skill Name

Experience
```

---

# 27. Mini Challenge

Create a new endpoint:

```http
GET /skills/advanced
```

Requirements:

* Use an aggregation pipeline.
* Filter only:

```text
level = "Advanced"
```

* Return only:

```json
{
    "name": "...",
    "category": "...",
    "experience": ...
}
```

using `$match` and `$project`.

---

# 28. Best Practices

* Use `aggregate()` when multiple data transformations are required.
* Keep each pipeline stage focused on a single responsibility.
* Use `$project` to reduce unnecessary data transfer.
* Filter as early as possible with `$match` for better performance.

---

# 29. Summary

Today you learned the foundation of MongoDB's Aggregation Framework.

We covered:

* ✅ What Aggregation is
* ✅ Aggregation Pipeline
* ✅ `$match`
* ✅ `$project`
* ✅ Building aggregation-based repository methods
* ✅ Creating aggregation APIs

These two stages are the building blocks for every advanced aggregation pipeline.

---

# 🚀 Next Lesson (Most Important MongoDB Topic)

In the next lesson, we'll learn **`$lookup`**, which is MongoDB's equivalent of a SQL `JOIN`.

We'll finally combine our two collections:

```text
Users
        ▲
        │
        │ user_id
        │
Skills
```

and build APIs like:

```json
{
  "user": {
    "full_name": "Paindla Narendar Reddy",
    "email": "narendar@gmail.com"
  },
  "skills": [
    {
      "name": "Python",
      "level": "Advanced"
    },
    {
      "name": "FastAPI",
      "level": "Intermediate"
    }
  ]
}
```

This is one of the most commonly used MongoDB features in production backend development.
