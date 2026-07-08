# Module 15 – Part 7

# Modifying Existing Columns (Real Industry) – Why Autogenerate Is Not Always Enough

Today you'll learn one of the biggest realities of backend development:

> **Alembic can detect many schema changes automatically, but not every change can be applied safely without developer review.**

This is exactly why **every migration should be reviewed before applying it**, especially in production.

---

# Real Industry Scenario

Our SmartCart API has been running for one year.

Current `users` table:

```text
users

------------------------------------
id
name
email
phone
```

Current model:

```python
class User(Base):

    __tablename__ = "users"

    id: Mapped[int] = mapped_column(Integer, primary_key=True)

    name: Mapped[str] = mapped_column(String(100))

    email: Mapped[str] = mapped_column(String(150))

    phone: Mapped[str] = mapped_column(
        String(20),
        nullable=True
    )
```

Everything works.

---

# New Business Requirement

The Product Manager says:

> Every user must provide a phone number during registration.

Current situation

```text
phone

NULL Allowed
```

New requirement

```text
phone

NULL Not Allowed
```

---

# Step 1

Modify Model

Old

```python
phone: Mapped[str] = mapped_column(
    String(20),
    nullable=True
)
```

New

```python
phone: Mapped[str] = mapped_column(
    String(20),
    nullable=False
)
```

Only one word changed.

```text
True

↓

False
```

Looks simple.

But production is different.

---

# Student Question

> Sir, why can't Alembic simply change it?

Let's see.

Database currently contains

| id | name     | phone      |
| -- | -------- | ---------- |
| 1  | Narendar | 9876543210 |
| 2  | Rahul    | NULL       |
| 3  | Priya    | NULL       |

Notice

Two users have

```text
NULL
```

If MySQL executes

```sql
ALTER TABLE users
MODIFY phone VARCHAR(20) NOT NULL;
```

Database says

> ❌ I cannot make this column NOT NULL because NULL values already exist.

Migration fails.

---

# Real Industry Solution

Companies never do this in one step.

Instead they follow a safe migration strategy.

---

## Phase 1

Allow NULL

```text
phone

NULL Allowed
```

Users continue using application.

---

## Phase 2

Backfill Data

Developers run a script

```python
for user in users:

    if user.phone is None:

        user.phone = "Pending"
```

or obtain real values through business workflows before enforcing the constraint.

Now database becomes

| id | phone      |
| -- | ---------- |
| 1  | 9876543210 |
| 2  | Pending    |
| 3  | Pending    |

No NULL values remain.

---

## Phase 3

Now execute migration

```text
NULL

↓

NOT NULL
```

Now migration succeeds.

---

# Lesson

Never think only about

```python
Model
```

Always think about

```text
Existing Data
```

---

# Another Example

Increase Phone Length

Current

```python
phone = mapped_column(String(20))
```

Requirement

International numbers

```python
phone = mapped_column(String(50))
```

Generate migration

```bash
alembic revision --autogenerate -m "Increase phone length"
```

Alembic generates

```python
def upgrade():

    op.alter_column(
        "users",
        "phone",
        type_=sa.String(50)
    )
```

SQL becomes

```sql
ALTER TABLE users
MODIFY phone VARCHAR(50);
```

Easy.

Usually safe.

---

# Another Requirement

Rename Column

Current

```text
phone
```

New

```text
mobile_number
```

Student thinks

```python
phone

↓

mobile_number
```

Alembic will detect rename.

Actually

No.

Alembic usually sees this as:

```text
Old Column Missing

+

New Column Added
```

It cannot reliably infer that you intended a rename.

---

Suppose

Old

```python
phone = mapped_column(...)
```

New

```python
mobile_number = mapped_column(...)
```

Autogenerate may produce something equivalent to:

```python
def upgrade():

    op.add_column(...)

    op.drop_column(...)
```

Students become happy.

Looks correct.

Actually

Very dangerous.

---

Imagine production data

| id | phone      |
| -- | ---------- |
| 1  | 9876543210 |
| 2  | 9999999999 |

Migration

```python
op.add_column(...)

op.drop_column(...)
```

Result

```text
phone

Deleted
```

All phone numbers are lost.

---

# Correct Way

Use rename operation supported by your database.

For example, with supported dialects:

```python
op.alter_column(
    "users",
    "phone",
    new_column_name="mobile_number"
)
```

Now

Data stays.

Only column name changes.

---

# Lesson

Autogenerate is

Excellent

But

Not Perfect.

Always review generated migration scripts before running them.

---

# Another Requirement

Change Data Type

Current

```python
price = mapped_column(Integer)
```

Manager says

```text
Need Decimal Prices
```

New

```python
price = mapped_column(Numeric(10,2))
```

Alembic generates

```python
op.alter_column(...)
```

SQL

```sql
ALTER TABLE products
MODIFY price DECIMAL(10,2);
```

Before applying this in production, think about:

* Do existing values convert correctly?
* Will any data be truncated?
* Is the application ready for decimal values?

---

# Visual Workflow

```text
Change Model

↓

Autogenerate

↓

Review Migration

↓

Edit If Required

↓

Apply Migration

↓

Verify Database
```

Notice

Never

```text
Autogenerate

↓

Production
```

without review.

---

# Real Company Story

Developer changes

```python
email

↓

email_address
```

Autogenerate produces

```python
add_column()

drop_column()
```

Developer blindly executes

```bash
alembic upgrade head
```

Production

```text
All Email Addresses Lost
```

Reason

He never reviewed migration.

This is why senior developers always inspect migration files.

---

# Student Checklist

Whenever Alembic generates a migration, ask yourself:

✅ Is data being deleted?

✅ Is a column being dropped?

✅ Is data being renamed or recreated?

✅ Will existing records still be valid?

✅ Can this migration run safely on a production database with millions of rows?

---

# Types of Changes

| Change                 | Usually Autogenerated          | Needs Review            |
| ---------------------- | ------------------------------ | ----------------------- |
| Add Column             | ✅ Yes                          | Always review           |
| Drop Column            | ✅ Yes                          | Yes (risk of data loss) |
| Change Length          | ✅ Usually                      | Yes                     |
| Change NULL Constraint | ✅ Often                        | Yes (existing data)     |
| Rename Column          | ❌ Often detected as add + drop | Manual correction       |
| Change Data Type       | ✅ Often                        | Yes (compatibility)     |

---

# Golden Rule

Think like this:

```text
SQLAlchemy Models

↓

Alembic Suggests

↓

Developer Reviews

↓

Developer Approves

↓

Database Changes
```

Alembic is an assistant.

**You are responsible for the final migration.**

---

# What We Learned Today

You now understand:

* Why changing `nullable` can fail on existing data.
* How production teams use multi-step migrations.
* How `op.alter_column()` is used.
* Why column renames need special care.
* Why blindly trusting autogenerate is risky.
* Why every migration should be reviewed before deployment.

---

# Next Lesson (Production-Level Schema Evolution Patterns)

We'll cover advanced production migration strategies, including:

1. Expand–Migrate–Contract pattern
2. Zero-downtime database migrations
3. Data migrations vs. schema migrations
4. Large table migration strategies
5. Rolling deployments with database changes
6. Safe deployment order (Application ↔ Database)
7. Real production migration checklist used by backend teams

This is the level of knowledge expected from backend engineers working on high-traffic applications.
