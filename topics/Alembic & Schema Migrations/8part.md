# Module 15 – Part 8

# Production Schema Evolution Patterns (How Companies Like Amazon, Netflix, Flipkart Handle Database Changes)

Today we'll learn something that separates a **Junior Developer** from a **Senior Backend Engineer**.

Most beginners think:

```text
Change Model

↓

Generate Migration

↓

Upgrade Database

↓

Done
```

This works for small projects.

But in real companies, databases contain:

* 5 million users
* 50 million orders
* 500 million products

A wrong migration can bring the entire application down.

So companies follow **Schema Evolution Patterns**.

---

# Real SmartCart Scenario

Current User table

```text
users

--------------------------------
id
name
email
phone
```

Application Version

```text
v1.0
```

Everything is working.

---

## New Requirement

Manager says

> We want users to login using **mobile_number** instead of **phone**.

A beginner immediately changes

```python
phone
```

↓

```python
mobile_number
```

Runs

```bash
alembic revision --autogenerate
```

Alembic generates

```python
op.add_column(...)
op.drop_column(...)
```

Runs

```bash
alembic upgrade head
```

Production

↓

All phone numbers disappear.

Disaster.

---

# So what do companies do?

They follow

# Expand → Migrate → Contract Pattern

This is one of the most important database migration patterns.

```
Expand

↓

Migrate

↓

Contract
```

Let's understand using SmartCart.

---

# Phase 1 — Expand

Never remove anything first.

Instead

Expand the schema.

Current table

```text
users

id
name
email
phone
```

Migration

```python
mobile_number = mapped_column(
    String(20),
    nullable=True
)
```

Generate migration

```bash
alembic revision --autogenerate -m "Add mobile_number"
```

Apply

```bash
alembic upgrade head
```

Database becomes

```text
users

id
name
email
phone
mobile_number
```

Notice

Nothing is removed.

Old application still works.

New application also works.

Everyone is happy.

---

# Why?

Imagine

Production

```
100 Servers
```

Updating all servers takes time.

Server 1

```text
Old Code
```

Server 2

```text
New Code
```

Server 3

```text
Old Code
```

Server 4

```text
New Code
```

During deployment,

both application versions may run simultaneously.

If you delete

```text
phone
```

Old servers immediately fail.

---

# Phase 2 — Migrate Data

Now copy old data.

Example

```text
phone

↓

mobile_number
```

A simple migration script (conceptually):

```python
for user in users:

    user.mobile_number = user.phone
```

Now database

| phone      | mobile_number |
| ---------- | ------------- |
| 9876543210 | 9876543210    |
| 9999999999 | 9999999999    |

No data lost.

---

# Phase 3 — Update Application

Old application

```python
user.phone
```

New application

```python
user.mobile_number
```

Deploy new application.

Now

Nobody uses

```text
phone
```

anymore.

---

# Phase 4 — Contract

After confirming:

✅ All servers updated

✅ No old code exists

✅ Data copied successfully

Now remove old column.

Migration

```python
op.drop_column(
    "users",
    "phone"
)
```

Database becomes

```text
users

id
name
email
mobile_number
```

Done.

Notice

Deletion happened at the end.

Not the beginning.

---

# Expand → Migrate → Contract Diagram

```text
Original

phone

↓

Expand

phone
mobile_number

↓

Copy Data

phone
mobile_number

↓

Application Uses mobile_number

↓

Delete phone

↓

Finished
```

This pattern avoids downtime and data loss.

---

# Pattern 2

## Add New Required Column

Manager says

Every order must contain

```text
delivery_date
```

Beginner

```python
delivery_date = mapped_column(
    Date,
    nullable=False
)
```

Production fails.

Why?

Existing orders have

```
NULL
```

delivery dates.

---

# Correct Pattern

Step 1

```python
nullable=True
```

↓

Deploy

↓

Fill existing data

↓

Make

```python
nullable=False
```

This is a two-step migration.

---

# Pattern 3

## Large Table Migration

Imagine

```
orders

100 Million Rows
```

Adding an index.

Student thinks

```bash
alembic upgrade head
```

Done.

Actually

Database may lock the table while creating the index, depending on the database engine and options used.

If customers try placing orders during that lock,

application performance may degrade or operations may block.

Senior developers schedule such operations carefully or use database-specific online index features when available.

---

# Pattern 4

## Data Migration vs Schema Migration

Students often confuse these.

### Schema Migration

Changes structure.

Example

```text
Add Column

Create Table

Drop Table

Rename Column
```

Example

```python
op.add_column(...)
```

---

### Data Migration

Changes data.

Example

```
phone

↓

mobile_number
```

or

```
Inactive Users

↓

Active Users
```

or

```
USD

↓

INR
```

Notice

Structure doesn't change.

Only data changes.

---

# SmartCart Example

Schema Migration

```python
op.add_column(
    "users",
    sa.Column(...)
)
```

Data Migration

```python
UPDATE users

SET mobile_number = phone;
```

Very different.

---

# Pattern 5

## Backward Compatibility

Imagine deployment

Old Application

↓

New Database

Should work.

New Application

↓

Old Database

Should also work during rollout, when possible.

That is called

```text
Backward Compatibility
```

Good migrations preserve compatibility during deployment.

---

# Pattern 6

## Deployment Order

Students ask

Should I deploy

Application first

or

Database first?

Answer

Depends on the change, but for additive changes (like new nullable columns), a common safe order is:

```
Database

↓

Application
```

Why?

Application expects

new column.

Database already has it.

Everything works.

Removing columns usually happens **after** all application instances stop using them.

---

# Pattern 7

## Never Edit Old Migration Files

Suppose Git contains

```
001_create_users.py

002_add_phone.py

003_create_products.py
```

Beginner edits

```
001_create_users.py
```

Production

↓

Different history.

Other developers

↓

Different history.

Chaos.

---

Correct way

Create

```
004_modify_users.py
```

Never rewrite migration history that has already been shared or applied.

Think

Git Commit.

Once pushed,

Don't rewrite history casually.

---

# Pattern 8

## One Logical Change = One Migration

Bad

```
Create Products

Add Phone

Delete Orders

Rename User

Create Coupons
```

One migration.

Impossible to review.

Good

```
001_create_users

002_add_phone

003_create_products

004_create_coupons
```

Easy to rollback.

Easy to debug.

Easy to review.

---

# Production Checklist

Before running

```bash
alembic upgrade head
```

Every backend developer should ask:

### ✅ Is data going to be deleted?

### ✅ Can old application still work?

### ✅ Is rollback possible?

### ✅ Have I reviewed the generated migration?

### ✅ Have I tested this migration on a staging database?

### ✅ Will this lock a very large table?

### ✅ Does this migration preserve existing data?

### ✅ Is there a backup or recovery plan for production?

---

# Complete Production Workflow

```text
Business Requirement

↓

Modify SQLAlchemy Model

↓

Generate Migration

↓

Review Migration

↓

Test on Local Database

↓

Test on Staging Database

↓

Backup Production (if required)

↓

Deploy Migration

↓

Deploy Application

↓

Monitor Logs

↓

Verify Database

↓

Complete
```

Notice

Migration is only one step.

Deployment involves many safety checks.

---

# Junior vs Senior Thinking

| Junior Developer               | Senior Backend Engineer                 |
| ------------------------------ | --------------------------------------- |
| Generates migration            | Reviews migration carefully             |
| Trusts autogenerate completely | Treats autogenerate as a starting point |
| Thinks only about models       | Thinks about existing production data   |
| Focuses on code                | Focuses on data safety and rollback     |
| Tests locally                  | Tests in staging before production      |
| Deletes columns immediately    | Uses Expand → Migrate → Contract        |

---

# Key Takeaways

1. **Schema evolution is about protecting existing data**, not just changing models.
2. Use **Expand → Migrate → Contract** for breaking changes like renames.
3. Separate **schema migrations** from **data migrations**.
4. Never modify old migrations that have already been applied or shared.
5. Always review autogenerated migrations before running them.
6. Think about deployment, rollback, and compatibility—not just code.

---

# Next Lesson

We'll dive into **Alembic Downgrade and Rollback Strategies**, including:

* `alembic downgrade`
* `alembic downgrade -1`
* Downgrading to a specific revision
* `alembic history`
* `alembic current`
* `alembic heads`
* `alembic branches`
* Recovering from failed migrations
* Production rollback strategies with real SmartCart examples
