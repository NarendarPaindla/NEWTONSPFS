# SQLAlchemy 2.0 Course

# **Module: SQLAlchemy I**

# **Topic 3: Connection Setup (Part 2)**

> We are **continuing the same topic (Connection Setup)**. We will **not** move to Declarative Models until Connection Setup is completely finished.

---

# 7. Complete Hands-on Practical

Let's create a professional project structure.

```
student_management/

│
├── app/
│   │
│   ├── __init__.py
│   ├── database.py
│   ├── models.py
│   ├── crud.py
│   ├── config.py
│   └── main.py
│
├── requirements.txt
│
├── .gitignore
│
├── .env
│
└── README.md
```

---

## Why Each File Exists

### database.py

Contains:

* Engine
* Database URL
* Session Factory (later)
* Base Class (later)

This file is the heart of database communication.

---

### models.py

Contains

```
Student

Teacher

Course

Employee
```

Each class represents one table.

---

### crud.py

Contains

```
Create Student

Read Student

Update Student

Delete Student
```

Keeps database logic separate.

---

### config.py

Contains configuration.

Example

```
Database URL

Debug

App Name
```

---

### main.py

Starts the application.

---

# 8. Virtual Environment

Professional developers **never install packages globally**.

Instead:

```
One Project

↓

One Virtual Environment

↓

Own Packages
```

---

## Create Virtual Environment

### Windows

```bash
python -m venv .venv
```

---

### Linux

```bash
python3 -m venv .venv
```

---

## Activate

Windows

```bash
.venv\Scripts\activate
```

Linux

```bash
source .venv/bin/activate
```

---

After activation

```
(.venv)
```

appears before the terminal prompt.

---

# Why Virtual Environment?

Imagine:

Project A

```
SQLAlchemy 2.0
```

Project B

```
Old SQLAlchemy
```

Without virtual environments

↓

Version conflict.

With virtual environments

↓

Each project has its own dependencies.

---

# 9. Installation

## Install SQLAlchemy

```bash
pip install "sqlalchemy>=2.0,<3.0"
```

---

## Verify

```bash
pip show sqlalchemy
```

Example output

```
Name: SQLAlchemy

Version: 2.x.x
```

---

## SQLite

SQLite requires **no additional installation** because Python ships with the built-in `sqlite3` module.

---

## PostgreSQL

Install the modern driver:

```bash
pip install psycopg[binary]
```

---

## MySQL

One common option:

```bash
pip install pymysql
```

---

# 10. Understanding Database URL

One of the most confusing topics for beginners.

Let's understand it slowly.

---

## SQLite URL

```
sqlite:///student.db
```

Break it down.

```
sqlite

↓

Database Type
```

```
:///

↓

Current Project Folder
```

```
student.db

↓

Database File
```

Complete meaning

```
Use SQLite

Store data inside

student.db
```

---

## PostgreSQL URL

```
postgresql+psycopg://username:password@localhost:5432/college
```

Breakdown

| Part       | Meaning       |
| ---------- | ------------- |
| postgresql | Database      |
| psycopg    | Driver        |
| username   | Database user |
| password   | Password      |
| localhost  | Server        |
| 5432       | Port          |
| college    | Database      |

---

## MySQL URL

```
mysql+pymysql://root:password@localhost:3306/college
```

Exactly the same structure.

---

# URL Diagram

```
Database Type

↓

Driver

↓

Username

↓

Password

↓

Host

↓

Port

↓

Database Name
```

---

# Examples

SQLite

```
sqlite:///college.db
```

PostgreSQL

```
postgresql+psycopg://admin:admin123@localhost:5432/student_db
```

MySQL

```
mysql+pymysql://root:root@localhost:3306/student_db
```

---

# 11. create_engine()

Now let's study the most important function.

```python
from sqlalchemy import create_engine
```

This imports the engine creation function.

---

Now

```python
DATABASE_URL = "sqlite:///student.db"
```

Store the database location.

---

Now

```python
engine = create_engine(DATABASE_URL)
```

This creates an Engine object.

---

Think of Engine as

```
Database Manager
```

---

# Internal Diagram

```
DATABASE_URL

↓

create_engine()

↓

Engine Object

↓

Ready to Obtain Connections

↓

Database
```

---

# Complete Code

```python
# Import SQLAlchemy engine creator
from sqlalchemy import create_engine

# Database URL
DATABASE_URL = "sqlite:///student.db"

# Create Engine
engine = create_engine(DATABASE_URL)
```

---

# Line-by-Line Explanation

### Line 1

```python
from sqlalchemy import create_engine
```

Import engine creation function.

---

### Line 2

```python
DATABASE_URL="sqlite:///student.db"
```

Tell SQLAlchemy

Use SQLite.

Create/open

student.db.

---

### Line 3

```python
engine=create_engine(DATABASE_URL)
```

Engine object is created.

Think

```
Python

↓

Engine

↓

Ready

↓

Database
```

No SQL has been executed yet.

---

# Does create_engine() Create the Database?

Great beginner question.

Answer:

**It depends on the database.**

### SQLite

If `student.db` does not exist, SQLite typically creates the database file when a connection is first established.

### PostgreSQL / MySQL

The database itself must already exist. `create_engine()` will not create a new PostgreSQL or MySQL database for you.

---

# Does Engine Immediately Connect?

Many beginners think

```
create_engine()

↓

Database Connected
```

Wrong.

Actual process

```
create_engine()

↓

Engine Object Created

↓

Connection Requested Later

↓

Connection Opened
```

This is called **lazy connection**.

The Engine prepares everything, but it doesn't immediately open a permanent database connection.

---

# 12. Dry Run

Suppose Python starts.

Memory

```
Empty
```

---

Step 1

```python
DATABASE_URL="sqlite:///student.db"
```

Memory

```
DATABASE_URL

↓

"sqlite:///student.db"
```

---

Step 2

Python executes

```python
engine=create_engine(DATABASE_URL)
```

Memory

```
DATABASE_URL

↓

Engine Object
```

---

Engine stores

```
Database Type

SQLite

↓

Database File

student.db
```

---

Still

No SQL.

No Connection.

No Tables.

---

Later

Application needs database.

```
Engine

↓

Obtains Connection

↓

Database

↓

Result
```

---

# 13. Visual Flow

```
Python Starts

↓

Read DATABASE_URL

↓

Create Engine

↓

Application Runs

↓

Needs Database

↓

Engine Obtains Connection

↓

Database

↓

SQL Executes

↓

Result Returned
```

---

# 14. Advantages

## Database Independence

One codebase can support SQLite, PostgreSQL, or MySQL by changing the database URL and installing the appropriate driver.

---

## Centralized Configuration

Only one place stores database settings.

---

## Cleaner Code

The rest of your application doesn't need to know how the connection is established.

---

## Reusability

One Engine object can be reused throughout the application.

---

## Efficient Resource Management

The Engine manages connections efficiently and typically uses **connection pooling** (except for some SQLite configurations).

We'll learn pooling later.

---

# 15. Disadvantages

## Wrong URL

A typo in the URL means the application cannot connect.

---

## Wrong Driver

Installing the wrong database driver causes connection failures.

---

## Authentication Errors

Incorrect username or password prevents database access.

---

## Network Problems

For remote databases,

Internet/network issues can stop connections.

---

# 16. Best Practices

Senior developers

✅ Store URLs in `.env`

Never hardcode passwords.

---

✅ One Engine per application.

---

✅ Keep `database.py` responsible only for database configuration.

---

✅ Use environment variables for production.

---

✅ Prefer SQLAlchemy 2.x APIs.

---

# 17. Common Beginner Mistakes

### Mistake 1

Hardcoding passwords inside source code.

Wrong

```python
DATABASE_URL = "postgresql://admin:123456@localhost/db"
```

Better

Read from environment variables (we'll cover this in later lessons).

---

### Mistake 2

Creating multiple Engine objects unnecessarily.

Wrong

```python
engine1 = create_engine(...)
engine2 = create_engine(...)
engine3 = create_engine(...)
```

Prefer a single shared Engine for the application.

---

### Mistake 3

Confusing the Engine with a database connection.

Remember:

```
Engine

≠

Connection
```

The Engine manages connections.

---

# 18. Interview Questions

## Beginner

### Q1

What is `create_engine()`?

**Answer**

It creates a SQLAlchemy Engine object that manages communication with the database.

---

### Q2

Does `create_engine()` immediately connect?

**Answer**

No. It prepares the Engine. A connection is typically obtained only when the application needs one.

---

## Intermediate

### Q3

What is a database URL?

**Answer**

A string that specifies the database type, driver, host, credentials (if needed), port, and database name or file.

---

### Q4

Why use a virtual environment?

**Answer**

To isolate project dependencies and avoid version conflicts.

---

## Advanced

### Q5

What is lazy connection?

**Answer**

The Engine doesn't immediately establish a permanent connection when created. It obtains a connection when database work is requested.

---

# 19. Real Project Example

Suppose we're building an **Employee Management System**.

```
Employee App

↓

database.py

↓

Engine

↓

PostgreSQL

↓

employees Database
```

All modules share the same Engine instead of creating separate ones.

---

# 20. Industry Notes

### Startups

Often begin with SQLite for local development and later move to PostgreSQL without major application changes because SQLAlchemy abstracts much of the database interaction.

### Enterprise Companies

* Store database URLs in environment variables or secret managers.
* Use connection pooling.
* Monitor database connections.
* Avoid embedding credentials in source code.

---

# 21. Summary

## Short Summary

Connection Setup prepares SQLAlchemy to communicate with a database through an Engine and the appropriate database driver.

---

## One-Line Revision

> **Engine manages database communication; it is not the same as a database connection.**

---

## Key Points

* Database URL identifies the database.
* `create_engine()` creates an Engine.
* Engine manages connections.
* SQLite uses the built-in `sqlite3` driver.
* PostgreSQL and MySQL require external drivers.
* Use virtual environments and environment variables.

---

## Mind Map

```
Connection Setup
│
├── Virtual Environment
├── SQLAlchemy Installation
├── Database URL
├── Driver
├── create_engine()
├── Engine
├── Lazy Connection
└── Best Practices
```

---

# 22. Practice Exercises

### Easy

1. Write the SQLite database URL for a file named `college.db`.
2. Explain what an Engine is in your own words.
3. List three parts of a PostgreSQL database URL.

### Medium

1. Compare SQLite and PostgreSQL connection URLs.
2. Explain why `create_engine()` doesn't immediately connect.

### Hard

Design the `database.py` file structure for a Hospital Management System, listing what responsibilities it should have.

---

# 23. Assignment

You have joined a software company.

Your task is to prepare a database setup document that includes:

* Project folder structure
* Why a virtual environment is needed
* SQLAlchemy installation steps
* SQLite, PostgreSQL, and MySQL URL formats
* Explanation of `create_engine()`
* Difference between an Engine and a database connection
* Best practices for storing database credentials

---

# 24. Mini Quiz (No Answers Yet)

1. What is the purpose of a database connection?
2. What does `create_engine()` return?
3. Is an Engine the same as a Connection?
4. What is a database URL?
5. Which driver is used for SQLite?
6. Why is a virtual environment recommended?
7. Does `create_engine()` create PostgreSQL databases?
8. What is lazy connection?
9. Why should you avoid hardcoding credentials?
10. Where should database configuration usually be placed in a project?

---

# 25. Quiz Answers

1. To allow the application to communicate with the database.
2. An Engine object.
3. No. The Engine manages connections; it is not a connection itself.
4. A string describing how to connect to the database.
5. Python's built-in `sqlite3` module.
6. To isolate dependencies and avoid version conflicts.
7. No. The PostgreSQL database must already exist.
8. The Engine obtains a connection only when database work is needed.
9. For security and easier configuration management.
10. In a dedicated module such as `database.py`, with sensitive values loaded from configuration or environment variables.

---

# 26. Revision Notes

```
Database URL
      │
      ▼
create_engine()
      │
      ▼
Engine
      │
      ▼
Obtains Connection
      │
      ▼
Database
      │
      ▼
SQL Executes

Remember:
Engine ≠ Connection
Connection ≠ Database
Driver enables communication
```

---

# 27. Professional Tips

1. **Create only one Engine per application** unless you intentionally connect to multiple databases.
2. **Never commit passwords to Git.** Use `.env` files locally and secret managers in production.
3. **Understand the connection lifecycle.** Creating an Engine is inexpensive; opening and maintaining database connections is the more significant resource.
4. **Use SQLite for learning and local prototypes**, but plan for PostgreSQL in production applications that require stronger concurrency and scalability.
5. **Always verify your database URL first** when troubleshooting connection issues—it is one of the most common sources of errors.

---

