Excellent. I will continue with your new rule.

✅ One lesson = One complete file

✅ No partial code

✅ Complete replaceable file

✅ Based on your current project

---

# Module 6 — Part 4

## Complete Updated File

**File**

```text
app/services/user_service.py
```

Replace your existing file completely with the following.

```python
from bson import ObjectId

from fastapi import HTTPException
from fastapi import status

from app.core.security import hash_password

from app.enums.roles import UserRole

from app.repositories.user_repository import (
    user_repository
)


class UserService:

    async def create_user(
        self,
        user
    ):

        existing_email = await user_repository.get_by_email(
            user.email
        )

        if existing_email:
            raise HTTPException(
                status_code=status.HTTP_400_BAD_REQUEST,
                detail="Email already exists"
            )

        existing_username = await user_repository.get_by_username(
            user.username
        )

        if existing_username:
            raise HTTPException(
                status_code=status.HTTP_400_BAD_REQUEST,
                detail="Username already exists"
            )

        data = user.model_dump()

        data["password"] = hash_password(
            user.password
        )

        data["role"] = UserRole.USER.value

        result = await user_repository.create(
            data
        )

        return result

    async def get_users(
        self
    ):
        return await user_repository.get_all()

    async def get_user(
        self,
        object_id: ObjectId
    ):

        user = await user_repository.get_by_id(
            object_id
        )

        if user is None:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="User not found"
            )

        return user

    async def update_user(
        self,
        object_id: ObjectId,
        user
    ):

        existing_user = await user_repository.get_by_id(
            object_id
        )

        if existing_user is None:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="User not found"
            )

        email_user = await user_repository.get_by_email(
            user.email
        )

        if (
            email_user
            and email_user["_id"] != object_id
        ):
            raise HTTPException(
                status_code=status.HTTP_400_BAD_REQUEST,
                detail="Email already exists"
            )

        username_user = await user_repository.get_by_username(
            user.username
        )

        if (
            username_user
            and username_user["_id"] != object_id
        ):
            raise HTTPException(
                status_code=status.HTTP_400_BAD_REQUEST,
                detail="Username already exists"
            )

        update_data = user.model_dump()

        update_data["password"] = existing_user["password"]

        update_data["role"] = existing_user.get(
            "role",
            UserRole.USER.value
        )

        await user_repository.update(
            object_id,
            update_data
        )

        return await user_repository.get_by_id(
            object_id
        )

    async def delete_user(
        self,
        object_id: ObjectId
    ):

        existing_user = await user_repository.get_by_id(
            object_id
        )

        if existing_user is None:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="User not found"
            )

        await user_repository.delete(
            object_id
        )

    async def update_role(
        self,
        object_id: ObjectId,
        role: UserRole
    ):

        existing_user = await user_repository.get_by_id(
            object_id
        )

        if existing_user is None:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="User not found"
            )

        await user_repository.update(
            object_id,
            {
                **existing_user,
                "role": role.value
            }
        )

        return await user_repository.get_by_id(
            object_id
        )


user_service = UserService()
```

---

# What Changed?

## Registration

Now every new user automatically becomes

```python
UserRole.USER
```

No client can become Admin.

---

## Password

Still hashed using

```python
hash_password()
```

---

## Update User

Cannot accidentally remove

* password
* role

Both are preserved.

---

## Duplicate Validation

Still validates

* Email
* Username

during update.

---

## New Method

```python
update_role()
```

This will be used later by

```http
PATCH /users/{id}/role
```

Admin only.

---

# MongoDB Document

New users will now be stored as

```json
{
    "_id": ObjectId(),

    "full_name": "Narendar",

    "username": "narendar",

    "email": "narendar@gmail.com",

    "password": "$2b$12$...",

    "bio": "Backend Trainer",

    "location": "Hyderabad",

    "role": "USER"
}
```

---

# Best Practices Used

✅ Password hashed

✅ Default USER role

✅ Preserve role during update

✅ Preserve password during update

✅ Duplicate validation

✅ Proper HTTP status codes

---

# Interview Questions

1. Why assign the role in the service instead of the schema?
2. Why preserve the existing password during profile updates?
3. Why preserve the existing role during updates?
4. Why is `update_role()` separate from `update_user()`?
5. Why shouldn't registration accept a role field?

---

# Summary

Today we updated the complete **`user_service.py`**.

New features:

* ✅ Automatic USER role assignment
* ✅ Role update support
* ✅ Secure profile updates
* ✅ Password preservation
* ✅ Duplicate validation improvements

---

## 🚀 Next Lesson (One Complete File)

The next complete file will be:

```text
app/dependencies/permissions.py
```

This will introduce **Role-Based Access Control (RBAC)** with reusable dependencies like:

* `get_current_admin()`
* `require_role()`
* `require_admin()`

These will let us protect routes using `Depends()` instead of scattering role checks throughout the code, making the authorization layer clean and reusable.
