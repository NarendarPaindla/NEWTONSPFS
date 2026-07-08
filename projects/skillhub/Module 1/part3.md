# Backend Development with FastAPI + MongoDB

# **Module 1 — Part 3**

# **Building a Professional Project Structure & Configuration**

> **Industry Rule #1:** Before writing business logic, build a project that is easy to maintain.

---

# Learning Objectives

After this lesson, you will be able to:

* Understand why professional projects are organized into folders.
* Build a scalable FastAPI project structure.
* Configure the application using environment variables.
* Understand why hardcoding configuration is a bad practice.
* Use **Pydantic v2 Settings** to manage configuration.
* Create reusable configuration objects.
* Prepare the project for MongoDB integration.

---

# Before We Start

## Think Like a Software Engineer

Imagine you are asked to build an application with **100 APIs**.

If everything is inside one file:

```python
main.py
```

Eventually, it grows to:

```text
main.py
-------------
6500 Lines
-------------
```

Now ask yourself:

* Where is the login code?
* Where is the database code?
* Where is the user API?
* Where is JWT?
* Where is MongoDB?

You will spend more time searching than coding.

This is why companies **never** build everything in one file.

---

# Real Industry Folder Structure

Let's look at a professional backend.

```text
skillhub-api/
│
├── app/
│   ├── api/
│   ├── core/
│   ├── db/
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

At first glance, this may seem like a lot, but each folder has a single responsibility.

---

# Why So Many Folders?

Let's understand the purpose of each one.

## `api/`

Contains API endpoints.

Example:

```text
GET /skills
POST /users/login
DELETE /snippets
```

Nothing related to the database should be written here.

Think of it as the **Reception Desk**.

---

## `core/`

Contains the core configuration of the application.

Examples:

* App Settings
* Security
* JWT
* Configuration
* Logging

Think of it as the **Engine Room**.

---

## `db/`

Everything related to the database.

Later it will contain:

* MongoDB Connection
* Collections
* Database Initialization

---

## `models/`

MongoDB document models.

Example:

```text
User

Skill

Snippet

Comment
```

---

## `schemas/`

Request and Response models using Pydantic.

Example:

```python
UserCreate

UserLogin

SkillCreate

SkillResponse
```

---

## `services/`

Business logic.

Example:

Instead of writing:

```python
Create User
Hash Password
Store User
```

inside the API,

we write it inside Services.

Think of Services as the **Brain**.

---

## `repositories/`

Responsible for talking to MongoDB.

Example:

```text
Insert User

Find User

Delete User
```

Nothing else.

Think of Repository as the **Translator** between FastAPI and MongoDB.

---

## `dependencies/`

Reusable dependencies.

Example:

```python
Current User

Database Dependency

Admin Dependency
```

---

## `middleware/`

Runs before every request.

Examples:

* Logging
* Authentication
* Request Timer

---

## `utils/`

Utility functions.

Examples:

```python
Generate OTP

Random ID

Email Helper

Date Formatter
```

---

## `exceptions/`

Custom error handling.

Instead of writing errors everywhere,

we centralize them.

---

# Visual Architecture

```text
                Client

                   │

                   ▼

              API Layer

                   │

                   ▼

             Service Layer

                   │

                   ▼

          Repository Layer

                   │

                   ▼

               MongoDB
```

This is called **Layered Architecture**.

Almost every modern backend follows this design.

---

# Updating Our Project

Create these folders:

```text
skillhub-api/
│
├── app/
│   ├── api/
│   ├── core/
│   ├── db/
│   ├── models/
│   ├── schemas/
│   ├── services/
│   ├── repositories/
│   ├── dependencies/
│   ├── middleware/
│   ├── utils/
│   ├── exceptions/
│   └── main.py
```

We won't use every folder today, but we'll fill them gradually throughout the course.

---

# What is Configuration?

Every application needs configuration.

For example:

```text
Database URL

Secret Key

JWT Expiry

Project Name

API Version

Debug Mode
```

Where should these values be stored?

---

## Bad Practice

```python
DATABASE_URL = "mongodb://localhost:27017"

SECRET_KEY = "123456"

DEBUG = True
```

Problems:

* Passwords become visible.
* Hard to change between development and production.
* Unsafe to commit to GitHub.

---

## Industry Standard

Store configuration in a `.env` file.

Create a file named:

```text
.env
```

---

# Our First `.env`

```env
PROJECT_NAME=SkillHub API

PROJECT_VERSION=1.0.0

DEBUG=True

API_V1=/api/v1
```

Notice that there are no quotes around the values.

Later, we'll add MongoDB connection strings and JWT secrets here.

---

# Why Use Environment Variables?

Suppose your app moves to production.

Development:

```text
DEBUG=True
```

Production:

```text
DEBUG=False
```

Instead of changing the source code, you simply change the `.env` file.

---

# Reading Environment Variables

We already installed:

```text
pydantic-settings
```

Now create:

```text
app/
    core/
        config.py
```

---

# Code: `config.py`

```python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    project_name: str
    project_version: str
    debug: bool
    api_v1: str

    class Config:
        env_file = ".env"


settings = Settings()
```

---

# Line-by-Line Explanation

## Import

```python
from pydantic_settings import BaseSettings
```

`BaseSettings` automatically reads values from the `.env` file and converts them to the correct Python types.

---

## Create a Settings Class

```python
class Settings(BaseSettings):
```

This class defines the configuration for our application.

---

## Define Variables

```python
project_name: str
project_version: str
debug: bool
api_v1: str
```

These names must match the keys in the `.env` file (Pydantic matches them case-insensitively by default).

When `debug` is loaded, the string `"True"` becomes the Python boolean `True`.

---

## Tell Pydantic Where to Look

```python
class Config:
    env_file = ".env"
```

This instructs Pydantic to load values from the `.env` file in the project root.

---

## Create a Singleton Settings Object

```python
settings = Settings()
```

Now, anywhere in the project, you can import `settings` instead of reading the `.env` file repeatedly.

---

# Using the Configuration

Update `app/main.py`:

```python
from fastapi import FastAPI

from app.core.config import settings

app = FastAPI(
    title=settings.project_name,
    version=settings.project_version,
)


@app.get("/")
async def root():
    return {
        "message": f"Welcome to {settings.project_name}",
        "version": settings.project_version,
        "debug": settings.debug,
    }
```

---

# How It Works

```text
.env

      │

      ▼

config.py

      │

      ▼

settings Object

      │

      ▼

main.py
```

Instead of hardcoding values, every part of the application reads from the same configuration object.

---

# Expected Output

Open:

```text
http://127.0.0.1:8000/
```

Response:

```json
{
  "message": "Welcome to SkillHub API",
  "version": "1.0.0",
  "debug": true
}
```

Open Swagger UI:

```text
http://127.0.0.1:8000/docs
```

Notice that the API title and version now come from the `.env` file.

---

# Folder Structure After Today's Lesson

```text
skillhub-api/
│
├── app/
│   ├── api/
│   ├── core/
│   │   └── config.py
│   ├── db/
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

### 1. Validation Error on Startup

```text
ValidationError: field required
```

**Reason:** A required key is missing from `.env`.

**Fix:** Ensure all fields (`PROJECT_NAME`, `PROJECT_VERSION`, `DEBUG`, `API_V1`) are present.

---

### 2. `.env` Not Being Read

**Reason:** The `.env` file is not in the project root or is named incorrectly.

**Fix:** Place `.env` beside `requirements.txt` and name it exactly `.env`.

---

### 3. Wrong Data Type

```env
DEBUG=hello
```

Pydantic cannot convert `"hello"` into a boolean.

Use:

```env
DEBUG=True
```

or

```env
DEBUG=False
```

---

# Interview Questions

1. Why do professional projects use multiple folders?
2. What is the responsibility of the `services` layer?
3. What is the role of the `repositories` layer?
4. Why should secrets not be hardcoded?
5. What is the purpose of a `.env` file?
6. What does `BaseSettings` do?
7. Why create a single `settings` object?

---

# Assignment

1. Create the complete folder structure shown above.
2. Add the `.env` file with the project configuration.
3. Create `app/core/config.py`.
4. Update `main.py` to use `settings`.
5. Verify that the API title and version in Swagger UI come from the `.env` file.

---

# Mini Challenge

Add two new configuration values:

```env
PROJECT_DESCRIPTION=Developer Portfolio Platform

PROJECT_AUTHOR=Paindla Narendar Reddy
```

Then update the root endpoint to include them in the JSON response.

---

# Best Practices

* Keep secrets and configuration outside the source code.
* Give each folder a single responsibility.
* Import configuration through a shared `settings` object.
* Avoid hardcoding values that may change between environments.
* Build a clean architecture from the beginning instead of refactoring a messy project later.

---

# Summary

Today, we transformed our simple FastAPI application into a project with a professional structure. We introduced environment-based configuration using **Pydantic v2 Settings**, making the application easier to maintain and ready for different environments.

## Next Lesson

We'll begin **Module 2 — MongoDB Connection**, where we'll:

* Install and understand the **Motor** async driver.
* Create a reusable MongoDB connection.
* Connect FastAPI to MongoDB.
* Manage the connection lifecycle.
* Verify the database connection before building our first CRUD API.
