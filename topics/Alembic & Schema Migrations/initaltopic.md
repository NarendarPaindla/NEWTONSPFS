

---

# Module 15 : Database Migrations with Alembic

## Project

**SmartCart API (Production Ready E-Commerce Backend)**

Database:

* MySQL
* SQLAlchemy 2.0
* FastAPI

---

# What students usually do

Suppose we already created a User table.

```python
class User(Base):
    __tablename__="users"

    id=mapped_column(Integer,primary_key=True)
    name=mapped_column(String(100))
    email=mapped_column(String(150))
```

Now create tables

```python
Base.metadata.create_all(bind=engine)
```

Database becomes

```
users

-------------
id
name
email
```

Everything works.

---

## After one week...

Client says

> We also need phone number.

Developer changes

```python
class User(Base):

    __tablename__="users"

    id=mapped_column(Integer,primary_key=True)
    name=mapped_column(String(100))
    email=mapped_column(String(150))
    phone=mapped_column(String(20))
```

Now run

```python
Base.metadata.create_all(bind=engine)
```

Student expects

```
users

id
name
email
phone
```

But...

Nothing happens.

Database is still

```
id
name
email
```

Why?

---

# Why create_all() doesn't work?

Because

```
create_all()
```

only creates

> Missing Tables

It never changes existing tables.

It never

* add column
* remove column
* rename column
* change datatype

Otherwise production databases would become dangerous.

Imagine

```
10 lakh users
```

Suddenly

```
create_all()
```

drops something accidentally.

Entire company loses data.

SQLAlchemy intentionally avoids modifying existing tables.

---

# Then what do companies do?

Companies never manually write

```
ALTER TABLE
```

every time.

Instead they use

## Alembic

Alembic is SQLAlchemy's migration tool.

Think of it like

```
Git

but

for Database
```

Git stores

```
Version 1
Version 2
Version 3
```

Alembic stores

```
Database Version 1
Database Version 2
Database Version 3
```

Every database change becomes history.

---

# Real Industry Example

Day 1

```
users

id
name
email
```

Version

```
001
```

---

Day 5

Manager says

```
Add phone
```

Alembic creates

```
002_add_phone
```

---

Day 10

Manager says

```
Add address
```

Alembic creates

```
003_add_address
```

---

Day 20

Manager says

```
Make email unique
```

Alembic creates

```
004_email_unique
```

Everything becomes version controlled.

---

# Without Alembic

Developer A

```
ALTER TABLE users ADD phone;
```

Developer B

```
Forgot to run SQL
```

Production

Different

Testing

Different

Developer Machine

Different

Chaos.

---

# With Alembic

Everyone runs

```
alembic upgrade head
```

Every database becomes identical.

---

# Think Like This

Code

```
Git
```

Database

```
Alembic
```

---

# What Alembic Actually Does

Suppose current database

```
users

id
name
email
```

Model becomes

```python
phone=mapped_column(String(20))
```

Alembic compares

Database

↓

```
id
name
email
```

Model

↓

```
id
name
email
phone
```

Difference

↓

```
Need to add phone
```

Generates SQL

```sql
ALTER TABLE users
ADD phone VARCHAR(20);
```

Instead of writing manually.

---

# Migration Workflow

Every company follows the same cycle.

```
Change Model

↓

Generate Migration

↓

Review Migration

↓

Apply Migration

↓

Database Updated
```

Simple.

---

# What We Will Learn

Over the next lessons, we'll build the full migration workflow for **SmartCart API**:

1. **Alembic Setup** (install and initialize Alembic)
2. Configure Alembic with our FastAPI + SQLAlchemy project
3. Create the **first migration**
4. **Autogenerate** migrations from model changes
5. Review and edit generated migration scripts
6. Apply migrations with `upgrade`
7. Roll back changes with `downgrade`
8. Understand the `alembic_version` table
9. Handle schema evolution patterns (adding, renaming, altering, dropping)
10. Production migration practices and deployment strategies

---

# Hands-on Project Structure (We'll Build)

```
smartcart/
│
├── app/
│   ├── database.py
│   ├── models/
│   │    ├── user.py
│   │    ├── product.py
│   │    └── order.py
│   │
│   └── main.py
│
├── alembic/
│
├── alembic.ini
│
└── requirements.txt
```

At the end of this module, you'll be able to evolve the SmartCart database safely from development to production without losing data.

---

## Next

In the next lesson, we'll start with **Alembic Setup**:

* Install Alembic
* Initialize it in the project
* Understand every generated file and folder
* Configure it to work with SQLAlchemy 2.0 and the SmartCart project
* Run the first migration end to end.
