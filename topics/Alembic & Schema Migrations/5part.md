# Module 15 – Part 5

# Applying the First Migration (`alembic upgrade head`) – End-to-End

This is one of the most important classes in Alembic.

Up to now, we have only **generated a migration file**.

The database is **still unchanged**.

Many beginners think:

> "Sir, I generated the migration. Why isn't my table created?"

Because generating a migration and applying a migration are **two different steps**.

---

# Recap

Current situation:

Our model

```python
class User(Base):
    __tablename__ = "users"

    id = mapped_column(Integer, primary_key=True)
    name = mapped_column(String(100))
    email = mapped_column(String(150))
```

We generated

```bash
alembic revision --autogenerate -m "Create users table"
```

Alembic created

```text
alembic/
    versions/
        4f98ab27_create_users_table.py
```

But our database is still empty.

```
smartcart database

(No Tables)
```

---

# Student Question

> Sir, if Alembic already generated the file, why didn't it create the table?

Because Alembic follows **two separate responsibilities**.

## Step 1

Generate Instructions

↓

```python
def upgrade():
    op.create_table(...)
```

## Step 2

Execute Instructions

↓

Database changes

Exactly like software development.

Writing code

↓

Doesn't run automatically

You must execute it.

---

# Step 1

Run

```bash
alembic upgrade head
```

Students usually ask,

What does this mean?

Let's break it.

---

## upgrade

Means

```
Move Database Forward
```

Old Version

↓

New Version

---

## head

Means

```
Latest Version
```

Suppose migrations exist

```
001

↓

002

↓

003

↓

004
```

Head means

```
004
```

Always the newest migration.

So

```bash
alembic upgrade head
```

means

```
Take database

↓

Apply every pending migration

↓

Reach latest version
```

---

# What Happens Internally?

Suppose database is empty.

```
Database

↓

No Tables
```

Migration file

```python
def upgrade():

    op.create_table("users")
```

Now execute

```bash
alembic upgrade head
```

Alembic performs these steps.

---

## Step 1

Connect to Database

```
MySQL

↓

smartcart
```

using

```ini
sqlalchemy.url
```

inside

```
alembic.ini
```

---

## Step 2

Check Current Version

Student asks,

How does Alembic know which migrations already ran?

Excellent question.

It creates a special table.

---

# alembic_version Table

After the first upgrade,

database becomes

```
smartcart

users

alembic_version
```

Student asks,

> Sir, I never created this table.

Correct.

Alembic creates it automatically.

---

## Purpose

This table stores only one thing.

```
Current Database Version
```

Example

| version_num |
| ----------- |
| 4f98ab27    |

That's it.

No user data.

No products.

Only version history.

---

# Why is it Needed?

Imagine

Developer A

Database Version

```
001
```

Developer B

Database Version

```
003
```

How will Alembic know?

Answer

```
alembic_version
```

It checks

```
Current Version

↓

Apply Remaining Migrations
```

---

# Visual Example

Before

```
Database

(No Version)
```

Run

```bash
alembic upgrade head
```

After

```
users

alembic_version
```

Contents

```
version_num

4f98ab27
```

Now Alembic knows

Database is already at

Revision

```
4f98ab27
```

---

# Step 3

Execute `upgrade()`

Migration contains

```python
def upgrade():

    op.create_table(
        "users",
        ...
    )
```

Alembic converts

Python

↓

SQL

```sql
CREATE TABLE users(
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(150)
);
```

and sends it to MySQL.

---

# Important

We never wrote

```sql
CREATE TABLE
```

ourselves.

Alembic generated it.

---

# Step 4

Update Version Table

After successful execution,

Alembic writes

```
4f98ab27
```

into

```
alembic_version
```

This tells Alembic,

```
Migration Applied Successfully
```

---

# Complete Flow

```
Model

↓

Autogenerate

↓

Migration File

↓

upgrade()

↓

alembic upgrade head

↓

Execute SQL

↓

Create Tables

↓

Update alembic_version
```

---

# Real Industry Example

Imagine Amazon has 200 developers.

Developer A creates

```
Add Product Table
```

Developer B creates

```
Add Orders Table
```

Developer C pulls latest code.

He doesn't manually execute SQL.

He simply runs

```bash
alembic upgrade head
```

Immediately,

Alembic checks

```
Current Version

↓

Pending Migrations

↓

Apply One by One
```

Everyone now has the exact same database schema.

This is why teams avoid sharing SQL scripts over chat or email.

---

# Verify the Database

Open MySQL.

Run

```sql
SHOW TABLES;
```

Expected output

```text
+-------------------+
| Tables_in_smartcart |
+-------------------+
| users             |
| alembic_version   |
+-------------------+
```

Now inspect the `users` table.

```sql
DESCRIBE users;
```

Output

```text
+-------+--------------+
| Field | Type         |
+-------+--------------+
| id    | int          |
| name  | varchar(100) |
| email | varchar(150) |
+-------+--------------+
```

Inspect the version table.

```sql
SELECT * FROM alembic_version;
```

Output

```text
+-------------+
| version_num |
+-------------+
| 4f98ab27    |
+-------------+
```

---

# Student Question

> Sir, if I run `alembic upgrade head` again, will it create the table again?

No.

Alembic first checks

```
alembic_version
```

It sees

```
Already at Head
```

So nothing happens.

This prevents duplicate table creation and makes migrations **idempotent**—safe to run repeatedly because already-applied migrations are skipped.

---

# Common Mistakes

## Mistake 1

Delete

```
alembic_version
```

Result

Alembic loses track of applied migrations.

---

## Mistake 2

Edit database manually.

```
ALTER TABLE users ...
```

without creating a migration.

Now

```
Database

≠

Models

≠

Migration History
```

Eventually, autogeneration becomes unreliable because Alembic compares the current database schema with your models, not your manual changes.

---

## Mistake 3

Generate migration

but forget

```bash
alembic upgrade head
```

Migration file exists.

Database never changes.

---

# Key Difference

Many students confuse these two commands.

| Command                           | Purpose                                                        |
| --------------------------------- | -------------------------------------------------------------- |
| `alembic revision --autogenerate` | Creates a migration file by comparing models with the database |
| `alembic upgrade head`            | Executes pending migrations and updates the database           |

Think of it like this:

```
revision

↓

Write Instructions

upgrade

↓

Execute Instructions
```

---

# What We Learned Today

You now understand:

* Why generating a migration does not change the database.
* What `alembic upgrade head` does.
* What `head` means.
* How Alembic connects to MySQL.
* Why the `alembic_version` table exists.
* How Alembic converts Python migration operations into SQL.
* Why running `upgrade head` multiple times is safe.
* The complete lifecycle from model → migration → database update.

At this point, we have successfully created and applied our first migration.

---

# Next Lesson

We'll make our **first schema change** by adding a `phone` column to the `User` model. Then we'll:

1. Run `alembic revision --autogenerate`.
2. See exactly how Alembic detects the new column.
3. Examine the generated migration code line by line.
4. Apply it with `alembic upgrade head`.
5. Verify the updated schema in MySQL.

This is the workflow you'll use repeatedly throughout real-world projects.
