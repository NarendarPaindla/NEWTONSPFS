# SQLAlchemy 2.0 Course

# Module: SQLAlchemy I

# Topic 1: ORM Basics (Part 1)

---

# 1. Simple Meaning

Imagine you have a notebook where all your student records are stored.

Normally, to find a student, you have to manually search through every page.

An **ORM** works like a smart assistant.

You simply say:

> "Give me Rahul."

Instead of writing SQL queries yourself, ORM finds Rahul from the database and gives you a Python object.

In very simple words:

> **ORM allows us to work with database data using Python objects instead of writing SQL queries most of the time.**

---

# 2. Definition

## Technical Definition

ORM (Object Relational Mapping) is a programming technique that maps database tables to programming language classes and database rows to objects, allowing developers to perform database operations using object-oriented programming instead of writing raw SQL.

---

## Same Definition in Simple English

Instead of talking directly to the database in SQL language,

you talk to Python objects,

and SQLAlchemy translates your Python code into SQL automatically.

---

## Breaking the Word ORM

ORM stands for

```
O → Object

R → Relational

M → Mapping
```

Let's understand each one.

---

### Object

Python has objects.

Example

```python
class Student:
    pass

student = Student()
```

student is an object.

---

### Relational

A relational database stores data inside tables.

Example

```
Students
--------------------
ID   Name   Age

1    Rahul   20

2    Priya   21
```

This is called a relational database.

Examples

* SQLite
* PostgreSQL
* MySQL
* Oracle
* SQL Server

---

### Mapping

Mapping means

"connecting one thing with another."

Example

Python Object

```
Student(
    id=1,
    name="Rahul",
    age=20
)
```

maps to

Database Row

```
ID   Name   Age

1    Rahul   20
```

ORM creates this connection automatically.

---

# 3. Why Do We Need ORM?

Before ORM existed, developers wrote SQL manually.

Example

Suppose you want all students.

Without ORM

```sql
SELECT *
FROM students;
```

Need students older than 20?

```sql
SELECT *
FROM students
WHERE age > 20;
```

Update Rahul?

```sql
UPDATE students
SET age = 21
WHERE id = 1;
```

Delete Rahul?

```sql
DELETE
FROM students
WHERE id = 1;
```

Everything required SQL.

---

Now imagine a project with

```
250 tables

20 developers

500 SQL queries

Thousands of users
```

Problems start appearing.

---

## Problem 1

Too Much SQL

Example

```
SELECT

INSERT

UPDATE

DELETE

JOIN

GROUP BY

HAVING

ORDER BY

LIMIT

OFFSET

Subqueries

Window Functions
```

Every developer must remember SQL syntax.

---

## Problem 2

Different Databases

Today

SQLite

Tomorrow

PostgreSQL

Next year

Oracle

Some SQL syntax changes between databases.

Developers had to rewrite queries.

---

## Problem 3

Repeated Code

Every project contained code like

```
Connect Database

Execute Query

Fetch Rows

Close Connection

Handle Errors
```

Again.

Again.

Again.

Thousands of times.

---

## Problem 4

SQL Injection Risks

Suppose user enters

```
Rahul
```

Query

```sql
SELECT *
FROM students
WHERE name='Rahul';
```

Now imagine attacker enters

```
' OR 1=1 --
```

Your SQL becomes

```sql
SELECT *
FROM students
WHERE name=''
OR 1=1;
```

Boom.

Entire database data may be returned.

ORM helps reduce such risks by using parameterized queries internally.

---

## Problem 5

Object-Oriented Programming vs SQL

Python

```
Objects

Classes

Methods

Inheritance

Encapsulation
```

Database

```
Rows

Columns

Tables
```

They are completely different worlds.

ORM connects them.

---

## Problem 6

Maintenance Nightmare

Imagine changing

```
StudentName

↓

FullName
```

Without ORM,

hundreds of SQL queries must be edited.

With ORM,

often only the model definition changes (depending on how the application is structured).

---

## Problem 7

Developer Productivity

Company wants

```
Student Module

Teacher Module

Fees Module

Attendance Module

Library Module
```

Writing SQL everywhere becomes slow.

ORM makes development much faster for common operations.

---

# What Problem Does ORM Solve?

ORM solves the communication gap between

```
Python
↓

Database
```

Instead of

```
Python

↓

SQL

↓

Database
```

You write

```
Python

↓

ORM

↓

SQL

↓

Database
```

ORM becomes a translator.

---

# Real Example

Without ORM

```sql
INSERT INTO students
(name, age)
VALUES
('Rahul',20);
```

With ORM (SQLAlchemy 2.0 style)

```python
student = Student(name="Rahul", age=20)

session.add(student)
session.commit()
```

Much easier to read.

---

# 4. Real World Analogy

## Analogy 1 — Restaurant

Imagine you visit a restaurant.

Without ORM

You go directly into the kitchen.

```
Customer

↓

Kitchen

↓

Chef

↓

Food
```

You need to know

* recipes
* ingredients
* cooking process

Very difficult.

---

With ORM

```
Customer

↓

Waiter

↓

Kitchen

↓

Chef

↓

Food
```

The waiter is the ORM.

You simply say

```
One Veg Biryani
```

The waiter translates your request.

Similarly,

Python tells ORM,

ORM tells Database.

---

## Analogy 2 — Google Translate

Suppose

You know English.

Database knows SQL.

You don't know SQL.

ORM acts like Google Translate.

```
English

↓

Translator

↓

Japanese
```

Similarly

```
Python

↓

ORM

↓

SQL
```

---

## Analogy 3 — Amazon Shopping

Customer clicks

```
Buy Laptop
```

Customer never writes

```sql
INSERT INTO orders ...
```

Internally,

Amazon's backend converts your actions into database operations.

Developers often use ORMs to simplify these interactions.

---

## Analogy 4 — WhatsApp

When you send

```
Hi
```

You don't know

* TCP
* UDP
* Encryption
* Packets
* Servers

WhatsApp handles everything.

ORM also hides database communication complexity while still allowing advanced control when needed.

---

# 5. Where Is ORM Used in Real Companies?

Almost every modern backend application uses an ORM for a significant portion of its database access.

Examples include companies such as:

* Amazon
* Flipkart
* Swiggy
* Zomato
* Uber
* Netflix
* Google
* Microsoft

> Large companies may combine ORMs with carefully optimized raw SQL for performance-critical queries, but ORMs remain a core productivity tool.

### Example: Swiggy

When you place an order:

```
Order Object

↓

ORM

↓

INSERT INTO Orders

↓

Database
```

### Example: Uber

Booking a ride:

```
Ride()

↓

ORM

↓

Ride Table
```

### Example: Netflix

Watching a movie:

```
WatchHistory()

↓

ORM

↓

Watch_History Table
```

---

# 6. Internal Working (High-Level Overview)

When you write:

```python
student = Student(name="Rahul", age=20)
session.add(student)
session.commit()
```

Internally, SQLAlchemy performs many steps:

```
Step 1
You create a Python object.

        │
        ▼

Step 2
The object is tracked by the Session.

        │
        ▼

Step 3
SQLAlchemy recognizes it as a new record.

        │
        ▼

Step 4
It generates the appropriate SQL (such as an INSERT statement).

        │
        ▼

Step 5
The SQL is sent through a database connection.

        │
        ▼

Step 6
The database executes the SQL.

        │
        ▼

Step 7
The database returns the result (for example, a generated primary key).

        │
        ▼

Step 8
SQLAlchemy updates your Python object with any returned values.
```

At this stage, we've only introduced the overall workflow. In later topics (Engine, Session, Declarative Models, CRUD), we'll explore each step in detail.

---



(We will remain on **ORM Basics** until it is fully completed before moving to the next subtopic.)
