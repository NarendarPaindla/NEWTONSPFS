# Backend Development using FastAPI + MongoDB

# **Project-Based Course**

# Module 1 — Part 1

# Project Introduction

> **Project Name:** SkillHub API – Developer Portfolio & Learning Platform

---

# Learning Objectives

After completing this lesson, students will be able to:

* Understand what backend development is.
* Understand why APIs are needed.
* Understand how frontend and backend communicate.
* Understand what FastAPI is.
* Understand the complete project they are going to build.
* Install all required software.
* Create a Python virtual environment.
* Install project dependencies.
* Run the FastAPI server.
* Open and use Swagger UI.
* Understand the initial project structure.

---

# Before We Start Coding

Imagine you are building your own version of GitHub for students.

Students can:

* Create an account.
* Add programming skills.
* Upload code snippets.
* Showcase projects.
* Follow other developers.
* Bookmark useful snippets.
* Like and comment on posts.
* Search by technology.
* Build a public developer portfolio.

This is exactly what we are going to build.

By the end of the course, your backend will be capable of serving a real frontend application.

---

# Why This Project?

Many beginner courses use projects like:

* Todo App
* Student Management
* Employee Management

These projects become repetitive and don't expose students to modern backend architecture.

SkillHub introduces real-world concepts naturally:

* User Authentication
* JWT
* MongoDB
* Async Programming
* REST APIs
* Search
* Pagination
* Aggregation
* Security
* Deployment

These are skills expected from backend developers in the industry.

---

# What is a Backend?

Suppose you open a website like GitHub.

You click **Login**.

What actually happens?

```text
Browser

↓

User enters Email + Password

↓

Request sent to Backend API

↓

Backend checks database

↓

Database returns result

↓

Backend sends response

↓

Browser displays dashboard
```

The backend is responsible for:

* Processing requests.
* Applying business logic.
* Validating data.
* Storing data.
* Authenticating users.
* Returning responses.

Without a backend, most modern applications cannot function.

---

# What is an API?

API stands for **Application Programming Interface**.

Think of it as a waiter in a restaurant.

```text
Customer
    │
    ▼
 Waiter (API)
    │
    ▼
 Kitchen (Backend Logic)
    │
    ▼
 Food
    │
    ▼
 Customer
```

The frontend never talks directly to the database. It communicates with the backend through APIs.

Example:

```http
POST /login
```

Request:

```json
{
    "email": "narendar@example.com",
    "password": "password123"
}
```

Response:

```json
{
    "access_token": "eyJhbGciOi...",
    "token_type": "bearer"
}
```

---

# What is FastAPI?

FastAPI is a modern Python framework used to build APIs quickly and efficiently.

Why FastAPI?

* Easy to learn
* Very fast
* Automatic API documentation
* Built-in validation
* Type hints support
* Asynchronous programming support
* Production-ready

Companies use FastAPI for microservices, AI applications, internal tools, and high-performance APIs.

---

# Technologies We'll Use

| Technology       | Purpose              |
| ---------------- | -------------------- |
| Python           | Programming language |
| FastAPI          | Backend framework    |
| MongoDB          | NoSQL database       |
| Motor            | Async MongoDB driver |
| Pydantic v2      | Data validation      |
| JWT              | Authentication       |
| Passlib + Bcrypt | Password hashing     |
| OAuth2           | Secure login         |
| Uvicorn          | ASGI server          |

---

# Final Project Features

By the end of the course, SkillHub will support:

### User Features

* Register
* Login
* Logout
* Update Profile
* Public Portfolio

### Skill Features

* Add Skills
* Edit Skills
* Delete Skills
* Search Skills

### Code Snippet Features

* Create Snippets
* Syntax Highlighting Metadata
* Tags
* Categories

### Social Features

* Like
* Comment
* Bookmark
* Follow Developers

### Search Features

* Search by Technology
* Search by Tags
* Search by User

### Admin Features

* Manage Users
* Manage Categories
* Moderate Content

---

# Project Architecture

```text
                Client (Frontend)
                        │
                        ▼
                 FastAPI Backend
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
 Authentication     Business Logic   Validation
        │
        ▼
      MongoDB
```

This layered architecture keeps the application modular and maintainable.

---

# What We Will Build Step by Step

Instead of writing everything at once, we will grow the project incrementally:

```
Lesson 1
↓

Project Setup

↓

Lesson 2

Database Connection

↓

Lesson 3

CRUD APIs

↓

Lesson 4

More Collections

↓

Lesson 5

Authentication

↓

Lesson 6

Authorization

↓

Lesson 7

Async Programming

↓

Lesson 8

MongoDB Advanced

↓

Lesson 9

Production Improvements

↓

Lesson 10

Testing

↓

Lesson 11

Deployment
```

Each lesson builds on the previous one.

---

# Software Requirements

Install the following before we begin coding:

1. Python 3.12 or later
2. Visual Studio Code
3. MongoDB Community Server (or MongoDB Atlas later)
4. Git
5. Postman (optional, Swagger UI will be enough initially)

We'll verify each installation in the next lesson.

---

# Initial Folder Structure (Target)

Don't create everything yet—this is where we're heading.

```text
skillhub-api/
│
├── app/
│   ├── api/
│   ├── core/
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
├── tests/
├── .env
├── requirements.txt
├── README.md
└── .gitignore
```

We'll create these folders gradually instead of all at once.

---

# Real-World Development Workflow

In most companies, backend development follows this sequence:

1. Understand the business problem.
2. Design the API.
3. Design the database.
4. Implement features.
5. Test APIs.
6. Secure the application.
7. Optimize performance.
8. Deploy to production.

This course mirrors that workflow so you learn how real backend projects are built.

---

# Summary

In this lesson, you learned:

* What backend development is.
* What an API does.
* Why FastAPI is a strong choice.
* The purpose and scope of the SkillHub project.
* The technologies we'll use.
* The high-level project architecture.
* The roadmap for the entire course.

No code yet—just a clear understanding of **what** we're building and **why**. This foundation will make every upcoming lesson easier to understand.

---

# Interview Questions

1. What is backend development?
2. What is an API?
3. Why does a frontend need a backend?
4. What is FastAPI?
5. What are the advantages of FastAPI over traditional frameworks?
6. What role does MongoDB play in this project?

---

# Assignment

Write short answers (2–3 sentences each):

1. What problem does the SkillHub API solve?
2. Why do we use APIs instead of connecting the frontend directly to the database?
3. List the technologies used in this project and explain the purpose of each.

---

# Mini Challenge

Think of **three additional features** that could make SkillHub even more useful for developers. For each feature, briefly describe:

* What it does.
* Which users would use it.
* Why it adds value.

---

# Best Practices

* Understand the project before writing code.
* Learn the purpose of each technology, not just its syntax.
* Build software incrementally.
* Keep the folder structure organized from the beginning.
* Focus on understanding the flow before memorizing code.

---

## Next Lesson

We'll start coding by setting up the development environment:

* Installing Python and verifying it.
* Creating the project folder.
* Creating a virtual environment.
* Installing FastAPI, Uvicorn, Motor, and other packages.
* Creating `requirements.txt`.
* Writing the first FastAPI application.
* Running the server.
* Exploring Swagger UI.

From the next lesson onward, we'll write code in every session and continue building the same **SkillHub API** project from scratch.
