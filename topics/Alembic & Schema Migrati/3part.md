# Module 15 – Part 3

# Configure Alembic with SQLAlchemy (Hands-on)

Today we are going to connect **Alembic** with our **SmartCart API**.

Until now,

```text
Alembic
```

doesn't know

* Which database?
* Which models?
* Which tables?
* Which metadata?

So if we generate a migration now,

```bash
alembic revision --autogenerate -m "Initial Migration"
```

Alembic has nothing to compare.

It is like asking a teacher to check an exam paper without giving the answer key.

---

# Current Project Structure

```text
smartcart/

│── app/
│     │── database.py
│     │── main.py
│     │
│     ├── models/
│     │      ├── user.py
│     │      ├── product.py
│     │      └── order.py
│
│── alembic/
│      │── env.py
│      │── versions/
│
│── alembic.ini
```

---

# Step 1

## Open `alembic.ini`

You will find

```ini
sqlalchemy.url = driver://user:pass@localhost/dbname
```

This is only a placeholder.

Replace it with your database URL.

```ini
sqlalchemy.url = mysql+pymysql://root:password@localhost/smartcart
```

Now Alembic knows **where** the database is.

---

## Question

Why can't Alembic use `database.py` directly?

Because Alembic runs as a separate CLI program.

When you execute

```bash
alembic upgrade head
```

Alembic starts independently.

It doesn't automatically execute

```python
app/main.py
```

or

```python
database.py
```

Therefore, we must tell Alembic how to connect.

---

# Step 2

Open

```text
alembic/env.py
```

Initially you'll see something similar to:

```python
from alembic import context
from sqlalchemy import engine_from_config
from sqlalchemy import pool

config = context.config

target_metadata = None
```

---

## Question

Why is `target_metadata = None`?

Because Alembic doesn't know our models yet.

Think of it like this:

Database

↓

Current Structure

Model

↓

Expected Structure

Alembic compares both.

Without model metadata,

comparison is impossible.

---

# Step 3

Import Base

Our Base class is inside

```python
app/database.py
```

```python
class Base(DeclarativeBase):
    pass
```

Import it.

```python
from app.database import Base
```

Now change

```python
target_metadata = None
```

to

```python
target_metadata = Base.metadata
```

---

## What is `Base.metadata`?

This is one of the most important concepts in SQLAlchemy.

When we create a model

```python
class User(Base):
    __tablename__="users"

    id = mapped_column(...)
```

SQLAlchemy stores this information inside

```python
Base.metadata
```

Think of it as a catalog.

```
Base.metadata

↓

users

↓

columns

↓

constraints

↓

indexes
```

Alembic reads this catalog.

---

# But There Is a Problem

Suppose your project contains

```text
models/

user.py

product.py

order.py
```

None of these files are imported.

So what happens?

```
Base.metadata

↓

Empty
```

Why?

Because Python only registers models after importing them.

---

# Demonstration

Suppose

```python
# user.py

class User(Base):
    ...
```

If nobody imports `user.py`

Python never executes

```python
class User(Base)
```

Therefore

```python
Base.metadata.tables
```

will be empty.

---

# Solution

Import every model inside `env.py`.

```python
from app.models.user import User
from app.models.product import Product
from app.models.order import Order
```

Notice something.

We never use

```python
User
```

inside the file.

Students usually ask:

> Sir, why import something we never use?

Excellent question.

The purpose is not to use the variable.

The purpose is to execute the file.

When Python imports

```python
user.py
```

this line runs

```python
class User(Base):
```

Immediately,

SQLAlchemy registers the table.

Now

```python
Base.metadata
```

contains

```
users

products

orders
```

Now Alembic can compare them.

---

# Visual Representation

Without imports

```
Alembic

↓

Base.metadata

↓

Nothing
```

With imports

```
Import user.py

↓

Import product.py

↓

Import order.py

↓

Base.metadata

↓

users

products

orders
```

---

# Complete Top Portion of `env.py`

```python
from alembic import context
from sqlalchemy import engine_from_config
from sqlalchemy import pool

from app.database import Base

from app.models.user import User
from app.models.product import Product
from app.models.order import Order

config = context.config

target_metadata = Base.metadata
```

This is enough for Alembic to understand your schema.

---

# How Alembic Thinks

Imagine the database currently contains

```
users

id

name

email
```

Your models contain

```
users

id

name

email

phone
```

Alembic compares

```
Database

↓

id
name
email
```

with

```
Model

↓

id
name
email
phone
```

Difference detected

↓

```
phone missing
```

Generated SQL

```sql
ALTER TABLE users
ADD phone VARCHAR(20);
```

This is the magic of autogeneration.

---

# Why is it called `target_metadata`?

Because this is Alembic's **target**.

Current Database

↓

Compared Against

↓

Target Metadata

So

```
Database

↓

Target Metadata

↓

Generate Difference
```

---

# Quick Verification

You can verify that your metadata contains all tables by temporarily adding:

```python
print(Base.metadata.tables.keys())
```

Expected output

```text
dict_keys([
    'users',
    'products',
    'orders'
])
```

If you get

```text
dict_keys([])
```

it means your models were not imported correctly.

---

# Common Beginner Mistakes

### Mistake 1

Forget to import models.

Result

```
No changes detected.
```

even though models exist.

---

### Mistake 2

Keep

```python
target_metadata = None
```

Autogenerate won't work.

---

### Mistake 3

Wrong database URL in

```ini
alembic.ini
```

Result

```
Connection Error
```

---

### Mistake 4

Run Alembic from the wrong directory.

Always execute commands from the project root:

```text
smartcart/
```

not

```text
app/
```

---

# End Result

Our configuration is now complete.

```
SQLAlchemy Models

↓

Base.metadata

↓

target_metadata

↓

Alembic

↓

Ready to Compare Database
```

At this point, Alembic knows:

* ✅ Which database to connect to
* ✅ Which SQLAlchemy metadata to inspect
* ✅ Which models exist
* ✅ How to compare the model schema with the actual database schema

---

# Next Lesson

We'll generate our **first migration** using:

```bash
alembic revision --autogenerate -m "create users table"
```

We'll inspect every line of the generated migration file, understand how `upgrade()` and `downgrade()` work, and then apply it to the database with `alembic upgrade head`. This is where the complete migration workflow begins.
