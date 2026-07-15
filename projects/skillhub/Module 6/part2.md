Perfect. I can now inspect your project directly.

From now on, we'll change the workflow exactly as you requested.

## New Course Rule

✅ I will **inspect your current project first**.

✅ I will provide **ONE COMPLETE FILE** per lesson.

✅ I will **never** give partial code for a file.

✅ I will **never** ask you to merge snippets.

✅ Every file I provide will be **copy-paste replaceable**.

---

# Module 6 — Part 2

We'll start with a **new file**.

## Create

```text
app/enums/roles.py
```

Since this file doesn't exist in your project, create the `enums` folder and add the following file.

```python
from enum import Enum


class UserRole(str, Enum):
    USER = "USER"
    ADMIN = "ADMIN"
```

---

# Why use Enum instead of strings?

Instead of writing

```python
role = "ADMIN"
```

or

```python
role = "admin"
```

or

```python
role = "Admin"
```

(which can introduce typos),

we use

```python
UserRole.ADMIN
```

and

```python
UserRole.USER
```

This gives:

* Type safety
* Auto-completion in IDEs
* Consistent values across the project
* Easier future expansion

Later, if we need more roles, we only update this file:

```python
class UserRole(str, Enum):
    USER = "USER"
    ADMIN = "ADMIN"
    TRAINER = "TRAINER"
    MODERATOR = "MODERATOR"
```

Everything else continues to work.

---

# Next Lesson

Now that the roles are defined, the next file will be the **complete updated**:

```text
app/schemas/user_schema.py
```

I inspected your current `user_schema.py`, so in the next lesson I'll provide the **entire updated file** (not snippets) with role support integrated while keeping it compatible with your existing project.
