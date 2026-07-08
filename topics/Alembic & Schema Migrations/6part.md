# Module 15 – Part 6

# Schema Evolution – Adding a New Column (Real Industry Workflow)

Today you'll learn the **most common migration** performed in real projects.

> **Adding a new column to an existing table.**

This is something backend developers do almost every week.

---

# Real Industry Scenario

Imagine the SmartCart project has been running in production for **6 months**.

Current database:

```text
users

-------------------------
id
name
email
```

Everything is working.

There are **5 lakh users** already registered.

Now the Product Manager comes and says:

> "From next release, we need users' phone numbers for OTP login."

As a developer, you update the model.

---

# Step 1 – Modify the SQLAlchemy Model

Current model:

```python
class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(Integer, primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    email: Mapped[str] = mapped_column(String(150))
```

Add the new field:

```python
class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(Integer, primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    email: Mapped[str] = mapped_column(String(150))
    phone: Mapped[str] = mapped_column(String(20), nullable=True)
```

Notice that **only Python code changed**.

The database has **not** changed yet.

---

# Student Question

> Sir, why did we use `nullable=True`?

Imagine your production database has:

| id | name     | email                                           |
| -- | -------- | ----------------------------------------------- |
| 1  | Narendar | [narendar@gmail.com](mailto:narendar@gmail.com) |
| 2  | Rahul    | [rahul@gmail.com](mailto:rahul@gmail.com)       |
| 3  | Priya    | [priya@gmail.com](mailto:priya@gmail.com)       |

These users already exist.

If you suddenly add a new column as:

```python
phone = mapped_column(String(20), nullable=False)
```

MySQL asks:

> "What phone number should I put for these existing users?"

It has no answer.

Migration may fail because existing rows would violate the NOT NULL constraint.

That's why, in many production systems, developers first add the column as nullable, backfill the data, and only then consider making it non-nullable.

---

# Visual Representation

Before

```text
users

id
name
email
```

Model becomes

```text
users

id
name
email
phone
```

Database is still

```text
users

id
name
email
```

Difference exists.

---

# Step 2 – Generate Migration

Run:

```bash
alembic revision --autogenerate -m "Add phone column to users"
```

Now Alembic performs comparison.

Database

↓

```text
id
name
email
```

Model

↓

```text
id
name
email
phone
```

Difference detected.

---

# What Does Alembic Generate?

Open the new migration file inside:

```text
alembic/
    versions/
```

You'll see something similar to:

```python
from alembic import op
import sqlalchemy as sa

revision = "8fd1ac90"
down_revision = "4f98ab27"
```

Notice

This is **not** the first migration anymore.

Now

```text
Revision Chain

4f98ab27

↓

8fd1ac90
```

Alembic knows:

> "This migration comes after the previous one."

---

# Understanding `upgrade()`

Alembic generates:

```python
def upgrade():

    op.add_column(
        "users",
        sa.Column(
            "phone",
            sa.String(length=20),
            nullable=True
        )
    )
```

Let's understand every line.

---

## `op`

`op` means

```text
Operations
```

It provides functions such as:

```text
op.create_table()

op.add_column()

op.drop_column()

op.create_index()

op.drop_index()

op.alter_column()
```

Think of `op` as Alembic's toolbox.

---

## `add_column`

```python
op.add_column(...)
```

Means:

```text
ALTER TABLE users
ADD COLUMN phone ...
```

You never write SQL yourself.

Alembic generates it.

---

## `sa.Column`

This is almost the same as writing:

```python
mapped_column(String(20))
```

inside SQLAlchemy models.

Alembic converts your model definition into a SQLAlchemy `Column` object for the migration.

---

# What SQL Will Actually Execute?

Internally, Alembic translates the migration into SQL similar to:

```sql
ALTER TABLE users
ADD COLUMN phone VARCHAR(20);
```

You didn't write this SQL.

Alembic generated it automatically.

---

# What About `downgrade()`?

Alembic also generates:

```python
def downgrade():

    op.drop_column("users", "phone")
```

Students often ask:

> Why remove it?

Imagine this situation:

Yesterday

Version 2 deployed.

Today

Customers report a serious bug related to phone login.

Management says:

> Roll back immediately.

Alembic executes:

```bash
alembic downgrade -1
```

This calls:

```python
def downgrade():

    op.drop_column("users", "phone")
```

Database returns to the previous version.

---

# Complete Flow

```text
Developer

↓

Modify Model

↓

Generate Migration

↓

Review Migration

↓

Upgrade Database

↓

Database Updated
```

---

# Step 3 – Apply the Migration

Run:

```bash
alembic upgrade head
```

Alembic executes:

```python
upgrade()
```

↓

Generates SQL

↓

Runs SQL

↓

Updates

```text
alembic_version
```

Now the database becomes:

```text
users

---------------------
id
name
email
phone
```

---

# Verify in MySQL

Run:

```sql
DESCRIBE users;
```

Expected output:

```text
+--------+--------------+
| Field  | Type         |
+--------+--------------+
| id     | int          |
| name   | varchar(100) |
| email  | varchar(150) |
| phone  | varchar(20)  |
+--------+--------------+
```

Migration completed successfully.

---

# Student Question

> Sir, if I run `revision --autogenerate` again without changing anything, what happens?

Excellent question.

Alembic compares:

Database

↓

```text
id
name
email
phone
```

Model

↓

```text
id
name
email
phone
```

No difference.

The generated migration will typically contain empty `upgrade()` and `downgrade()` methods because there are no schema changes to apply.

That's Alembic telling you:

> "Nothing has changed."

---

# Real Company Workflow

Suppose a sprint includes these tasks:

Monday

```text
Add phone column
```

Tuesday

```text
Add address column
```

Wednesday

```text
Create products table
```

Thursday

```text
Create orders table
```

Each change becomes **one migration**.

History looks like:

```text
001_create_users

↓

002_add_phone

↓

003_add_address

↓

004_create_products

↓

005_create_orders
```

This gives every developer and every environment the exact same schema history.

---

# Important Rule

**One logical schema change = One migration**

Avoid putting unrelated changes into a single migration.

For example:

❌ One migration that:

* Adds phone
* Creates products
* Drops address
* Renames email

Instead, create focused migrations:

* `add_phone_to_users`
* `create_products_table`
* `rename_email_column`

They are easier to review, test, and roll back if needed.

---

# What We Learned Today

You now understand:

* How to evolve an existing schema safely.
* Why production databases require migrations instead of `create_all()`.
* Why `nullable=True` is often used first when adding columns to tables with existing data.
* How Alembic detects changes using `--autogenerate`.
* How `op.add_column()` works.
* How Alembic converts migration operations into SQL.
* Why `downgrade()` is generated automatically.
* The recommended workflow for incremental schema evolution.

---

# Next Lesson

We'll cover **modifying existing columns**, including:

* Changing `nullable=True` to `nullable=False`
* Changing data types (for example, `VARCHAR(20)` → `VARCHAR(50)`)
* Renaming columns
* Why some changes cannot be fully autgenerated
* When and how to manually edit migration files
* Safe production strategies for evolving existing columns without data loss
