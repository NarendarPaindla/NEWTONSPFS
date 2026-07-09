# Module 15 – Part 4

# First Migration (Autogenerate) – End-to-End

Today is where students usually have the biggest **"Aha!"** moment.

For the first time, we'll see how **Alembic converts our SQLAlchemy models into SQL** automatically.

---

# Recap

So far we have:

```text
SmartCart/

│── app/
│      database.py
│      models/
│          user.py
│          product.py
│          order.py
│
│── alembic/
│      env.py
│      versions/
│
│── alembic.ini
```

We have already:

* ✅ Installed Alembic
* ✅ Initialized Alembic
* ✅ Configured `env.py`
* ✅ Connected `Base.metadata`

Now Alembic knows our models.

---

# Current User Model

Suppose we have only one model.

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

Notice something.

We have written only Python.

No SQL.

No CREATE TABLE.

Nothing.

---

# Student Question

> Sir, how does Alembic know what SQL to write?

Excellent question.

Alembic doesn't read MySQL directly.

It first reads

```text
Base.metadata
```

Inside metadata it finds

```text
users

↓

id

↓

name

↓

email
```

Then it compares

```text
Database

VS

Metadata
```

---

# Let's Check the Database

Suppose SmartCart database is completely empty.

```text
smartcart

(No Tables)
```

Our model

```text
users

id

name

email
```

Difference

```text
Database

↓

Nothing

Model

↓

users table
```

Alembic thinks

> I need to create this table.

---

# Step 1

Generate Migration

Run

```bash
alembic revision --autogenerate -m "Create users table"
```

Students usually panic seeing this command.

Let's understand every word.

---

## revision

Means

```text
Create a New Database Version
```

Exactly like

```text
Git Commit
```

---

## --autogenerate

This is the most powerful option.

Without it

Alembic creates an empty migration.

With it

Alembic compares

```text
Database

↓

Current

Model

↓

Expected
```

Then generates SQL automatically.

---

## -m

Means

```text
Message
```

Exactly like

```bash
git commit -m "Initial Commit"
```

Here

```bash
-m "Create users table"
```

is only for humans.

Later you'll understand the purpose immediately.

---

# What Happens Internally?

Suppose

Database

```text
(No Tables)
```

Model

```text
users
```

Alembic performs

```text
Step 1

Connect to Database

↓

Step 2

Read Current Tables

↓

Step 3

Read Base.metadata

↓

Step 4

Compare

↓

Step 5

Generate Python Migration File
```

Notice

It does **NOT**

modify database.

Students misunderstand this.

---

# Very Important

After running

```bash
alembic revision --autogenerate
```

Database is still

```text
Empty
```

Only a migration file is created.

---

# Look Inside versions/

Previously

```text
versions/

(empty)
```

Now

```text
versions/

4f98ab27_create_users_table.py
```

Question

Where did this strange filename come from?

---

# Revision ID

Alembic automatically generates

```text
4f98ab27
```

This is called

```text
Revision ID
```

Every migration has a unique ID.

Like Git Commit Hash.

Example

```text
001

↓

002

↓

003
```

Alembic instead uses

```text
4f98ab27

↓

8fd1ac90

↓

91bd72ef
```

to avoid conflicts.

---

# Open the Migration File

You'll see something like

```python
"""Create users table"""

from alembic import op
import sqlalchemy as sa

revision = "4f98ab27"

down_revision = None

branch_labels = None

depends_on = None
```

Students ask

Why so much code?

Let's understand.

---

# revision

```python
revision="4f98ab27"
```

Means

```text
Current Version
```

---

# down_revision

Since this is the first migration

```python
down_revision=None
```

Because

Nothing exists before this.

Later

Second migration

```python
revision="7bd29"

down_revision="4f98ab27"
```

Third migration

```python
revision="af732"

down_revision="7bd29"
```

Like a chain.

```text
V1

↓

V2

↓

V3

↓

V4
```

---

# Most Important Part

Now scroll further.

You'll see

```python
def upgrade():

    pass
```

or

```python
def upgrade():

    op.create_table(
        ...
    )
```

Depending on your model.

---

# What is upgrade()?

Think

Client wants

```text
New Feature
```

Database must move

Old Version

↓

New Version

Everything inside

```python
upgrade()
```

moves database

Forward.

---

Example

```python
def upgrade():

    op.create_table(...)
```

Means

```text
Database

↓

Create users table
```

---

# downgrade()

Below it

```python
def downgrade():
```

Students usually ignore this.

Actually

This is even more important.

Imagine

Yesterday

You deployed

```text
Version 5
```

Suddenly

Production crashes.

Company says

Rollback immediately.

Who saves us?

Answer

```python
downgrade()
```

---

Example

```python
def downgrade():

    op.drop_table("users")
```

Means

Undo

Whatever

upgrade()

did.

---

# Think Like CTRL + Z

Upgrade

```text
Old Version

↓

New Version
```

Downgrade

```text
New Version

↓

Old Version
```

Exactly

Undo.

---

# Complete Flow

Suppose

Model

```python
class User(Base):
```

↓

Alembic

↓

Creates Migration

↓

```python
def upgrade():

    op.create_table(...)
```

↓

Later

Run

```bash
alembic upgrade head
```

↓

Database

```text
users
```

created.

---

# Very Important Concept

Migration File is

NOT SQL.

It is

Python.

Example

```python
op.create_table(...)
```

Later Alembic converts it into

```sql
CREATE TABLE users(
...
)
```

and executes it.

So we write

Python

Alembic executes

SQL.

---

# Student Analogy

Imagine

You write

English

↓

Google Translate

↓

Telugu

Similarly

You write

```python
op.create_table()
```

↓

Alembic translates

↓

```sql
CREATE TABLE
```

↓

MySQL executes it.

---

# Industry Workflow

Developer writes model

↓

Runs

```bash
alembic revision --autogenerate -m "Create users table"
```

↓

Reviews migration file

↓

Commits migration to Git

↓

Another developer pulls code

↓

Runs

```bash
alembic upgrade head
```

↓

Everyone gets the same database schema.

---

# What We Learned Today

* Why `revision --autogenerate` is used.
* What `revision` means.
* What `--autogenerate` does internally.
* Why the `-m` message is useful.
* How Alembic compares the database with `Base.metadata`.
* The purpose of `revision` and `down_revision`.
* The role of `upgrade()` and `downgrade()`.
* Why migration files are Python code instead of raw SQL.

At this point, we have **generated** a migration, but we have **not yet changed the database**.

---

# Next Lesson

We'll execute the migration using:

```bash
alembic upgrade head
```

and explain:

* What `head` means
* How the `alembic_version` table is created
* What SQL Alembic actually executes
* How to verify the changes in MySQL
* The complete migration lifecycle from generation to database update.
