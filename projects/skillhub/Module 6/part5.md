Perfect. From now on, one lesson = one complete file.

Before giving the next file, I checked your project architecture. There is one thing I want to improve.

Instead of creating only `get_current_admin()`, we'll build a **generic permission dependency** that scales for future roles like `TRAINER`, `MODERATOR`, etc.

This is the same approach used in production FastAPI applications.

---

# Module 6 — Part 5

## Complete File

**Create New File**

```text
app/dependencies/permissions.py
```

Replace/Create this file with the following.

```python
from typing import Callable

from fastapi import Depends
from fastapi import HTTPException
from fastapi import status

from app.dependencies.auth import get_current_user
from app.enums.roles import UserRole


def require_roles(
    *allowed_roles: UserRole
) -> Callable:

    async def role_checker(
        current_user=Depends(
            get_current_user
        )
    ):

        user_role = current_user.get("role")

        allowed = [
            role.value
            for role in allowed_roles
        ]

        if user_role not in allowed:

            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail="You don't have permission to access this resource."
            )

        return current_user

    return role_checker


require_admin = require_roles(
    UserRole.ADMIN
)


require_user = require_roles(
    UserRole.USER,
    UserRole.ADMIN
)
```

---

# Why This Design?

Most beginners write

```python
async def get_current_admin():
```

Later they need

```python
get_current_trainer()

get_current_manager()

get_current_hr()

get_current_super_admin()
```

Very repetitive.

Instead

we create

```python
require_roles()
```

once.

Now

Admin API

```python
Depends(
    require_admin
)
```

Trainer API

```python
Depends(
    require_roles(
        UserRole.TRAINER
    )
)
```

Admin + Trainer

```python
Depends(
    require_roles(
        UserRole.ADMIN,
        UserRole.TRAINER
    )
)
```

No duplicate code.

---

# Example Usage

Only Admin

```python
@router.get("/users")

async def get_users(

    current_user=Depends(
        require_admin
    )

):
```

---

Admin or User

```python
@router.get("/profile")

async def profile(

    current_user=Depends(
        require_user
    )

):
```

---

Future

Admin + Moderator

```python
Depends(

    require_roles(

        UserRole.ADMIN,

        UserRole.MODERATOR

    )

)
```

Works immediately.

---

# Request Flow

```text
JWT

↓

get_current_user()

↓

role

↓

require_roles()

↓

Permission?

↓

YES

↓

API
```

---

# Advantages

✅ Reusable

✅ Clean

✅ Production Ready

✅ Easy to extend

✅ Single place to change authorization logic

---

# Best Practices

* Keep authentication (`get_current_user`) separate from authorization (`require_roles`).
* Use reusable dependencies instead of checking roles inside every route.
* Return **403 Forbidden** when a user is authenticated but lacks permission.
* Return the authenticated user from the dependency so routers can continue using it.

---

# Summary

Today we completed the authorization dependency layer.

We now have:

* ✅ Generic `require_roles()`
* ✅ `require_admin`
* ✅ `require_user`
* ✅ Reusable RBAC foundation

---

# 🚀 Next Lesson (One Complete File)

The next complete file will be:

```text
app/api/users.py
```

We'll update it completely to support:

* Admin-only `GET /users`
* Admin-only `DELETE /users/{id}`
* `GET /users/me`
* `PATCH /users/{id}/role`
* Proper RBAC using `require_admin`

After that, SkillHub will have fully working **Role-Based Access Control (RBAC)**.
