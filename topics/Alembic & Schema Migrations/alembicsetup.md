# Module 15 – Part 2

# Alembic Setup (Hands-on)

Now we'll start coding.

No shortcuts.

We'll understand **every file Alembic creates** and **why it exists**.

---

# Current SmartCart Project

```
smartcart/

│── app/
│     │── main.py
│     │── database.py
│     │
│     ├── models/
│     │      ├── user.py
│     │      ├── product.py
│     │      └── order.py
│     │
│     ├── routers/
│     ├── schemas/
│     ├── services/
│     └── core/

│── requirements.txt
```

Currently we have SQLAlchemy models.

Suppose User model

```python
# app/models/user.py

from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy import Integer,String

from app.database import Base

class User(Base):

    __tablename__="users"

    id:Mapped[int]=mapped_column(Integer,primary_key=True)
    name:Mapped[str]=mapped_column(String(100))
    email:Mapped[str]=mapped_column(String(150))
```

Database configuration

```python
# app/database.py

from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.orm import DeclarativeBase

DATABASE_URL="mysql+pymysql://root:password@localhost/smartcart"

engine=create_engine(DATABASE_URL)

SessionLocal=sessionmaker(bind=engine)

class Base(DeclarativeBase):
    pass
```

Everything works.

---

# Problem

We already know

```
Base.metadata.create_all()
```

only creates tables.

It cannot

```
Add Column

Delete Column

Rename Column

Change Datatype

Add Constraint
```

Hence we need Alembic.

---

# Step 1

## Install Alembic

Open terminal

```bash
pip install alembic
```

Also make sure these packages already exist

```bash
pip install sqlalchemy pymysql
```

Check installation

```bash
alembic --help
```

If successful

```
usage:

alembic

init

revision

upgrade

downgrade

history

current
```

means Alembic is installed.

---

# Why install separately?

Student question:

> Isn't Alembic included with SQLAlchemy?

Answer

No.

Think like this

```
SQLAlchemy

↓

Creates Models

↓

Executes Queries

↓

Handles ORM
```

Alembic

```
↓

Compares Models

↓

Creates Migration Scripts

↓

Version Controls Database
```

They are different tools.

---

# Step 2

Initialize Alembic

Inside project root

```
smartcart/
```

Run

```bash
alembic init alembic
```

Notice

Not

```
cd app
```

Always execute from project root.

---

# What happens internally?

Before command

```
smartcart/

app/
requirements.txt
```

Run

```bash
alembic init alembic
```

Now

```
smartcart/

app/

alembic/

alembic.ini
```

Alembic generated everything automatically.

Students usually ask

> Sir, why did it create so many files?

Let's understand.

---

# Folder 1

```
alembic/
```

Think of this folder as

```
Migration Project
```

Everything related to database versions stays here.

Never store business logic here.

---

Inside

```
alembic/

versions/

env.py

script.py.mako

README
```

We'll understand each one.

---

# versions/

Initially

```
versions/

(empty)
```

Student asks

> Why empty?

Because

No migrations exist yet.

Later

```
versions/

001_create_users.py

002_add_phone.py

003_add_products.py

004_create_orders.py
```

Every database change becomes one Python file.

Exactly like Git commits.

---

# env.py

This is

**The Brain of Alembic**

Every migration runs through this file.

Think

```
FastAPI

↓

main.py

↓

Application Starts
```

Similarly

```
Alembic

↓

env.py

↓

Migration Starts
```

This file tells Alembic

* Which database?
* Which models?
* Online migration?
* Offline migration?
* Metadata location?

Without env.py

Alembic doesn't know anything.

---

# script.py.mako

Students usually ignore this.

Actually

It is a

Template.

Whenever you create

```bash
alembic revision
```

Alembic copies this template.

Example

```
revision.py

↓

generated

↓

using

script.py.mako
```

Normally

Never modify it.

---

# README

Simple documentation.

Mostly ignored.

---

# File

```
alembic.ini
```

This file lives outside.

```
smartcart/

alembic.ini
```

Question

Why not inside alembic folder?

Because

This file stores

```
Project Configuration
```

Not migration code.

---

Open

```
alembic.ini
```

You'll see

```ini
script_location = alembic
```

Meaning

```
Migration folder

↓

alembic/
```

---

Another important line

```ini
sqlalchemy.url = driver://user:pass@localhost/dbname
```

Initially

```
Dummy URL
```

Example

```
driver://user:pass@localhost/dbname
```

This must be replaced.

We'll do that in the next lesson.

---

# Visual Understanding

```
SmartCart

│

├── app
│      Business Logic
│
├── alembic
│      Migration Engine
│
├── alembic.ini
│      Alembic Configuration
```

---

# Real Industry Analogy

Imagine a construction company.

```
Building

↓

Your Database
```

Architect

↓

SQLAlchemy Models

Construction History

↓

Alembic Versions

Construction Rules

↓

alembic.ini

Construction Supervisor

↓

env.py

Construction Diary

↓

versions/

```

Each new floor added to the building is like a new migration file. The building evolves safely without rebuilding it from scratch.

---

# What We Achieved Today

We learned:

- Why Alembic is a separate tool from SQLAlchemy.
- How to install Alembic.
- How to initialize it in a project.
- The purpose of the generated `alembic/` folder.
- The role of `versions/`, `env.py`, `script.py.mako`, `README`, and `alembic.ini`.

At this point, Alembic is installed, but it **still doesn't know about our SmartCart database or SQLAlchemy models**.

---

# Next Lesson

We'll configure Alembic to work with the SmartCart project:

- Connect Alembic to the MySQL database.
- Import SQLAlchemy `Base.metadata`.
- Update `env.py` correctly.
- Understand `target_metadata`.
- Explain online vs. offline migrations.
- Run the first configuration successfully before generating our first migration.
```
