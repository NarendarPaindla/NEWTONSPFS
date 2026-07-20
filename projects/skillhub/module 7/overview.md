Great. Since **Authentication, Users, and Skills** are completed, we'll continue building the **SkillHub API** by adding the next core module.

---

# Module 7 — Projects API

The **Projects API** is the next logical feature because every developer profile should showcase projects. It also introduces new concepts such as arrays, optional fields, URL validation, ownership, filtering, and searching.

---

# Learning Objectives

By the end of this module, students will be able to:

* Create a Project
* View all projects
* View their own projects
* View a specific project
* Update a project
* Delete a project
* Search projects
* Filter projects by technology
* Understand ownership and authorization
* Build production-ready CRUD APIs

---

# Why We Need This Module

Currently, a user can only list their skills.

A real developer portfolio should also display projects.

Example:

```
Narendar Reddy

Skills
-------
Python
FastAPI
MongoDB

Projects
---------
SkillHub API
Expense Tracker
AI Resume Analyzer
E-Commerce Backend
```

Now the application starts looking like a real developer portfolio instead of just a CRUD application.

---

# Features

Each user can:

* Create unlimited projects
* Edit only their own projects
* Delete only their own projects
* View everyone's public projects
* Search projects
* Filter by technology
* View only their own projects

---

# Project Entity

```
Project
```

---

# Fields

| Field       | Type         | Required | Description                   |
| ----------- | ------------ | -------- | ----------------------------- |
| id          | ObjectId     | Auto     | MongoDB ID                    |
| title       | string       | ✅        | Project title                 |
| description | string       | ✅        | Project description           |
| tech_stack  | list[string] | ✅        | Technologies used             |
| github_url  | string       | ❌        | GitHub repository             |
| live_url    | string       | ❌        | Live deployment URL           |
| image_url   | string       | ❌        | Project image                 |
| status      | string       | ✅        | Completed / Ongoing / Planned |
| featured    | bool         | ❌        | Featured project              |
| user_id     | ObjectId     | Auto     | Owner                         |
| created_at  | datetime     | Auto     | Creation time                 |
| updated_at  | datetime     | Auto     | Last update                   |

---

# MongoDB Document

```json
{
    "_id": ObjectId("..."),
    "title": "SkillHub API",
    "description": "Developer Portfolio Platform",
    "tech_stack": [
        "Python",
        "FastAPI",
        "MongoDB",
        "JWT"
    ],
    "github_url": "https://github.com/user/skillhub",
    "live_url": "https://skillhub.onrender.com",
    "image_url": "",
    "status": "Completed",
    "featured": true,
    "user_id": ObjectId("..."),
    "created_at": "...",
    "updated_at": "..."
}
```

---

# APIs We Will Build

## Public APIs

```
GET /projects

GET /projects/{id}

GET /projects/search

GET /projects/filter
```

---

## Protected APIs

```
POST /projects

PUT /projects/{id}

DELETE /projects/{id}

GET /projects/me
```

---

# Folder Structure Changes

```
app/

├── api/
│      └── projects.py
│
├── schemas/
│      └── project_schema.py
│
├── repositories/
│      └── project_repository.py
│
├── services/
│      └── project_service.py
```

---

# Concepts Covered

This module introduces several important backend concepts:

### CRUD Operations

* Create
* Read
* Update
* Delete

---

### Ownership Validation

A user can edit only their own project.

Example:

```
User A

↓

Project A

↓

Allowed
```

```
User B

↓

Project A

↓

403 Forbidden
```

---

### Arrays in MongoDB

Example:

```json
"tech_stack": [
    "Python",
    "FastAPI",
    "Redis",
    "Docker"
]
```

Students learn how MongoDB stores arrays naturally.

---

### URL Validation

Validate:

```
GitHub URL

Live URL

Image URL
```

---

### Search

Example

```
Python
```

Returns

```
Expense Tracker

SkillHub API

ChatBot API
```

---

### Filtering

Example

```
GET /projects?status=Completed
```

---

### Pagination

```
?page=1

&limit=10
```

---

### Sorting

```
Newest

Oldest

Featured
```

---

# Architecture

```
Client

↓

Projects API

↓

Project Service

↓

Project Repository

↓

MongoDB
```

---

# Files We Will Create

```
project_schema.py

↓

project_repository.py

↓

project_service.py

↓

projects.py
```

---

# Module Flow

We will build this module in the following order:

### Part 1

✅ Project Schemas

---

### Part 2

✅ Project Repository

---

### Part 3

✅ Project Service

---

### Part 4

✅ Project APIs

---

### Part 5

✅ Search

---

### Part 6

✅ Filtering

---

### Part 7

✅ Pagination

---

### Part 8

✅ Sorting

---

### Part 9

✅ Ownership Authorization

---

### Part 10

✅ Interview Questions

---

# Best Practices

* Keep business logic in the Service layer.
* Use the Repository layer only for database access.
* Validate URLs with Pydantic.
* Never accept `user_id` from the client; derive it from the authenticated user.
* Store timestamps in UTC.
* Use pagination for list endpoints.
* Return consistent response structures.

---

# What Students Will Learn by the End

Students will understand:

* Building a production-ready CRUD module
* Working with MongoDB arrays
* Ownership-based authorization
* Search and filtering
* Pagination and sorting
* Layered architecture (API → Service → Repository → Database)

---

## 🚀 Next Lesson

We will begin **Part 1** by creating the complete **`app/schemas/project_schema.py`** file, including:

* `ProjectBase`
* `ProjectCreate`
* `ProjectUpdate`
* `ProjectResponse`
* Enums for project status
* Pydantic validation for URLs
* Field constraints and examples

From this point onward, we'll build the **Projects API** exactly as we built the Users and Skills modules, with one complete file per lesson.
