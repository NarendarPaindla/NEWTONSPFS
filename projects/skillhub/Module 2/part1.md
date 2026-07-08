# Backend Development with FastAPI + MongoDB

# **Module 2 — Part 1**

# **Connecting FastAPI to MongoDB Using Motor (Async)**

> **Today is one of the most important lessons in the course.**
>
> Until now, our API could only return hardcoded data.
>
> **Today, we'll connect our backend to a real database.**

---

# Learning Objectives

By the end of this lesson, you will be able to:

* Understand what MongoDB is.
* Understand why we chose MongoDB.
* Understand what Motor is.
* Understand why FastAPI uses an async MongoDB driver.
* Install MongoDB.
* Connect FastAPI to MongoDB.
* Create a reusable database connection.
* Test the connection.
* Understand what happens behind the scenes.

---

# Before We Write Code...

## Imagine SkillHub Without a Database

User registers.

```
POST /register
```

Server Response:

```
Registration Successful
```

After 5 minutes...

Server restarts.

Everything disappears.

Why?

Because data was stored in **RAM (Memory)**.

Memory is temporary.

When the application stops...

```
RAM
↓

Everything Lost
```

That's why every backend needs a database.

---

# What is a Database?

A database stores data permanently.

Example:

```
User

↓

Name
Email
Password

↓

Stored Forever
```

Unlike RAM...

A database keeps data even after:

* Server Restart
* Laptop Restart
* Power Failure

---

# SQL vs NoSQL

Most beginners ask:

> "Why aren't we using MySQL?"

Good question.

## SQL Database

```
Table

+----+--------+------+
| ID | Name   | Age  |
+----+--------+------+
| 1  | Rahul  | 21   |
| 2  | Reddy  | 20   |
+----+--------+------+
```

Rows and columns.

Very structured.

---

## MongoDB (NoSQL)

Instead of tables...

MongoDB stores **Documents**.

```
{
    "name": "Narendar",
    "age": 24,
    "skills": [
        "Python",
        "FastAPI"
    ]
}
```

Documents are stored inside **Collections**.

Collections are stored inside **Databases**.

---

# MongoDB Terminology

| SQL      | MongoDB    |
| -------- | ---------- |
| Database | Database   |
| Table    | Collection |
| Row      | Document   |
| Column   | Field      |
| Join     | Lookup     |

Remember this table.

It is frequently asked in interviews.

---

# Why Did We Choose MongoDB?

Our SkillHub project contains:

* Users
* Skills
* Snippets
* Comments
* Likes
* Tags
* Bookmarks

Every user can have different numbers of:

* Skills
* Projects
* Social Links
* Badges

This data is flexible.

MongoDB is excellent for flexible and evolving data models.

---

# Project Architecture Today

Until yesterday:

```
Browser

↓

FastAPI

↓

JSON Response
```

Today:

```
Browser

↓

FastAPI

↓

MongoDB

↓

JSON Response
```

We are introducing the persistence layer.

---

# What is Motor?

Many students think:

> MongoDB Driver = Motor

Not exactly.

Let's understand.

Python cannot directly communicate with MongoDB.

It needs a driver.

Official synchronous driver:

```
PyMongo
```

Asynchronous driver:

```
Motor
```

Since FastAPI is asynchronous...

We use Motor.

---

# Why Not PyMongo?

Suppose 100 students send requests simultaneously.

With a synchronous driver:

```
Student 1

↓

Wait

↓

Student 2

↓

Wait

↓

Student 3
```

With Motor:

```
Student 1

Student 2

Student 3

Student 4

Student 5

↓

Database
```

Motor allows FastAPI to handle many requests efficiently.

We'll dive deeper into async programming in Module 7.

---

# MongoDB Hierarchy

```
MongoDB Server

↓

Database

↓

Collections

↓

Documents

↓

Fields
```

For SkillHub:

```
MongoDB

↓

skillhub_db

↓

users

↓

{
    "_id": "...",
    "name": "Narendar"
}
```

---

# Step 1 — Update `.env`

Add MongoDB settings.

```env
PROJECT_NAME=SkillHub API
PROJECT_VERSION=1.0.0
DEBUG=True
API_V1=/api/v1

MONGODB_URL=mongodb://localhost:27017
DATABASE_NAME=skillhub_db
```

---

# Why Store Database URL in `.env`?

Bad Practice:

```python
MONGODB_URL = "mongodb://localhost:27017"
```

Later in production:

```
MongoDB Atlas

↓

Different URL
```

If hardcoded...

You must edit your code.

Instead:

```
.env

↓

Change only one line

↓

Application Works
```

---

# Step 2 — Update `config.py`

```python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    project_name: str
    project_version: str
    debug: bool
    api_v1: str

    mongodb_url: str
    database_name: str

    class Config:
        env_file = ".env"


settings = Settings()
```

---

# Line-by-Line Explanation

These new fields:

```python
mongodb_url: str
database_name: str
```

tell Pydantic to read:

```
MONGODB_URL

DATABASE_NAME
```

from `.env`.

Now our application can access database configuration anywhere.

---

# Step 3 — Create `db` Package

Create:

```
app/
    db/
        database.py
```

Our project becomes:

```
app/
│
├── api/
├── core/
├── db/
│     database.py
├── models/
└── main.py
```

---

# Why Separate Database Code?

Never write MongoDB connection code inside:

```
main.py
```

Imagine later you have:

* 50 APIs
* 20 Collections
* Authentication
* Search

Database code mixed with API code becomes difficult to maintain.

That's why we isolate it in `db/database.py`.

---

# Step 4 — Create MongoDB Client

**File:** `app/db/database.py`

```python
from motor.motor_asyncio import AsyncIOMotorClient

from app.core.config import settings

client = AsyncIOMotorClient(settings.mongodb_url)

database = client[settings.database_name]
```

---

# Line-by-Line Explanation

## Import Motor Client

```python
from motor.motor_asyncio import AsyncIOMotorClient
```

Motor provides an asynchronous MongoDB client.

---

## Import Settings

```python
from app.core.config import settings
```

We reuse the configuration instead of hardcoding values.

---

## Create Client

```python
client = AsyncIOMotorClient(settings.mongodb_url)
```

This creates a connection object to the MongoDB server.

Important:

At this point, Motor does **not** immediately establish a network connection. It prepares the client, and the connection is opened lazily when the first database operation is performed.

---

## Select Database

```python
database = client[settings.database_name]
```

Think of this as selecting the `skillhub_db` database.

We will later access collections like:

```python
database.users

database.skills

database.snippets
```

---

# Visual Flow

```
.env

↓

Settings

↓

MongoDB URL

↓

AsyncIOMotorClient

↓

MongoDB Server

↓

Database
```

---

# Step 5 — Verify Database Connection

Update `app/main.py`:

```python
from fastapi import FastAPI

from app.core.config import settings
from app.db.database import database

app = FastAPI(
    title=settings.project_name,
    version=settings.project_version,
)


@app.get("/")
async def root():
    return {
        "message": "Welcome to SkillHub API"
    }


@app.get("/db-check")
async def db_check():
    collections = await database.list_collection_names()

    return {
        "database": settings.database_name,
        "collections": collections
    }
```

---

# What Happens Here?

```
GET /db-check

↓

FastAPI

↓

MongoDB

↓

List Collections

↓

Return JSON
```

Even if the database is empty, `list_collection_names()` returns an empty list instead of failing (assuming the server is reachable).

---

# Expected Output

Visit:

```
http://127.0.0.1:8000/db-check
```

First run:

```json
{
    "database": "skillhub_db",
    "collections": []
}
```

Later, after creating collections:

```json
{
    "database": "skillhub_db",
    "collections": [
        "users",
        "skills",
        "snippets"
    ]
}
```

---

# Folder Structure After Today

```text
skillhub-api/
│
├── app/
│   ├── api/
│   ├── core/
│   │    config.py
│   ├── db/
│   │    database.py
│   ├── models/
│   ├── schemas/
│   ├── services/
│   ├── repositories/
│   ├── dependencies/
│   ├── middleware/
│   ├── utils/
│   ├── exceptions/
│   └── main.py
│
├── .env
├── requirements.txt
└── README.md
```

---

# Common Errors

## 1. MongoDB Server Not Running

```
ServerSelectionTimeoutError
```

**Cause:** MongoDB service is stopped.

**Fix:**

* Start the MongoDB service.
* Or start the MongoDB Community Server manually.

---

## 2. Wrong MongoDB URL

```
Connection refused
```

**Cause:**

Incorrect host or port.

Check:

```env
MONGODB_URL=mongodb://localhost:27017
```

---

## 3. Module Not Found

```
No module named 'motor'
```

**Fix:**

```bash
pip install motor
```

---

## 4. Database Doesn't Exist

Students often ask:

> "I never created `skillhub_db`. Why didn't MongoDB complain?"

MongoDB creates databases and collections **automatically** when you first insert data into them.

Simply selecting a database with `client["skillhub_db"]` does not create it on disk.

---

# Interview Questions

1. What is MongoDB?
2. What is a document?
3. What is a collection?
4. Why is MongoDB called a NoSQL database?
5. Why do we use Motor instead of PyMongo with FastAPI?
6. What is `AsyncIOMotorClient`?
7. Why should the MongoDB URL be stored in `.env`?
8. When does MongoDB actually create a database?

---

# Assignment

1. Install MongoDB Community Server (if you haven't already).
2. Add `MONGODB_URL` and `DATABASE_NAME` to `.env`.
3. Update `config.py`.
4. Create `app/db/database.py`.
5. Create the `/db-check` endpoint.
6. Verify that it returns the database name and collection list.

---

# Mini Challenge

Create a new endpoint:

```http
GET /database-info
```

Return:

```json
{
    "database": "skillhub_db",
    "status": "Connected",
    "driver": "Motor",
    "async": true
}
```

Do not hardcode the database name—read it from the `settings` object.

---

# Best Practices

* Never hardcode connection strings.
* Keep database logic inside the `db` package.
* Use a single shared `AsyncIOMotorClient` for the entire application.
* Store configuration in `.env`.
* Verify connectivity early before implementing CRUD operations.

---

# Summary

Today we connected our FastAPI project to MongoDB using the **Motor** async driver. We learned why MongoDB is a good fit for SkillHub, how Motor differs from PyMongo, how to centralize database configuration, and how to verify that our application can communicate with the database.

## Next Lesson

We'll continue with **Module 2 — Part 2**, where we'll make our database integration more production-ready by:

* Managing the MongoDB connection using FastAPI's **lifespan** events.
* Checking the connection with a MongoDB `ping`.
* Closing the connection gracefully during application shutdown.
* Preparing the foundation for our first CRUD API.
