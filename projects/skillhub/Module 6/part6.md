Perfect. Since I have been following your uploaded project, the next file is the **complete updated `users.py`**.

This file introduces **Role-Based Access Control (RBAC)** using the `require_admin` dependency we created.

---

# Module 6 — Part 6

## Complete Updated File

**File**

```text
app/api/users.py
```

Replace your existing file completely with the following.

```python
from bson import ObjectId

from fastapi import APIRouter
from fastapi import Depends
from fastapi import HTTPException
from fastapi import status

from app.core.config import settings

from app.dependencies.auth import get_current_user
from app.dependencies.permissions import require_admin

from app.schemas.user_schema import (
    UserCreate,
    UserUpdate,
    UserRoleUpdate
)

from app.services.user_service import user_service

from app.utils.response import success_response
from app.utils.serializers import (
    user_serializer,
    user_serializer_list
)

router = APIRouter(
    prefix=f"{settings.api_prefix}/users",
    tags=["Users"]
)


@router.post(
    "/",
    status_code=status.HTTP_201_CREATED
)
async def create_user(
    user: UserCreate
):

    result = await user_service.create_user(
        user
    )

    return success_response(
        "User Registered Successfully",
        {
            "id": str(result.inserted_id)
        }
    )


@router.get(
    "/",
    dependencies=[
        Depends(require_admin)
    ]
)
async def get_users():

    users = await user_service.get_users()

    return success_response(
        "Users Retrieved Successfully",
        user_serializer_list(users)
    )


@router.get("/me")
async def get_current_profile(

    current_user=Depends(
        get_current_user
    )

):

    return success_response(
        "Current User",
        user_serializer(
            current_user
        )
    )


@router.get("/{user_id}")
async def get_user(

    user_id: str,

    current_user=Depends(
        get_current_user
    )

):

    if not ObjectId.is_valid(user_id):

        raise HTTPException(

            status_code=status.HTTP_400_BAD_REQUEST,

            detail="Invalid User ID"

        )

    user = await user_service.get_user(
        ObjectId(user_id)
    )

    if (
        current_user["role"] != "ADMIN"
        and str(current_user["_id"]) != user_id
    ):

        raise HTTPException(

            status_code=status.HTTP_403_FORBIDDEN,

            detail="You are not allowed to access this user."

        )

    return success_response(
        "User Retrieved Successfully",
        user_serializer(user)
    )


@router.put("/{user_id}")
async def update_user(

    user_id: str,

    user: UserUpdate,

    current_user=Depends(
        get_current_user
    )

):

    if not ObjectId.is_valid(user_id):

        raise HTTPException(

            status_code=status.HTTP_400_BAD_REQUEST,

            detail="Invalid User ID"

        )

    if (
        current_user["role"] != "ADMIN"
        and str(current_user["_id"]) != user_id
    ):

        raise HTTPException(

            status_code=status.HTTP_403_FORBIDDEN,

            detail="You are not allowed to update this user."

        )

    updated_user = await user_service.update_user(

        ObjectId(user_id),

        user

    )

    return success_response(

        "User Updated Successfully",

        user_serializer(updated_user)

    )


@router.delete(
    "/{user_id}",
    dependencies=[
        Depends(require_admin)
    ]
)
async def delete_user(
    user_id: str
):

    if not ObjectId.is_valid(
        user_id
    ):

        raise HTTPException(

            status_code=status.HTTP_400_BAD_REQUEST,

            detail="Invalid User ID"

        )

    await user_service.delete_user(
        ObjectId(user_id)
    )

    return success_response(
        "User Deleted Successfully"
    )


@router.patch(
    "/{user_id}/role",
    dependencies=[
        Depends(require_admin)
    ]
)
async def update_role(

    user_id: str,

    role: UserRoleUpdate

):

    if not ObjectId.is_valid(
        user_id
    ):

        raise HTTPException(

            status_code=status.HTTP_400_BAD_REQUEST,

            detail="Invalid User ID"

        )

    updated_user = await user_service.update_role(

        ObjectId(user_id),

        role.role

    )

    return success_response(

        "Role Updated Successfully",

        user_serializer(updated_user)

    )
```

---

# New APIs Added

## Register

```
POST /api/v1/users
```

Public

---

## Current User

```
GET /api/v1/users/me
```

Authenticated

---

## Get All Users

```
GET /api/v1/users
```

Admin Only

---

## Get User

```
GET /api/v1/users/{id}
```

Owner or Admin

---

## Update User

```
PUT /api/v1/users/{id}
```

Owner or Admin

---

## Delete User

```
DELETE /api/v1/users/{id}
```

Admin Only

---

## Change Role

```
PATCH /api/v1/users/{id}/role
```

Admin Only

---

# RBAC Rules

| API                | USER | ADMIN |
| ------------------ | ---- | ----- |
| Register           | ✅    | ✅     |
| Login              | ✅    | ✅     |
| My Profile         | ✅    | ✅     |
| Get Own Profile    | ✅    | ✅     |
| Get Any User       | ❌    | ✅     |
| Update Own Profile | ✅    | ✅     |
| Update Any User    | ❌    | ✅     |
| Delete User        | ❌    | ✅     |
| Change Role        | ❌    | ✅     |

---

# Best Practices Used

* ✅ RBAC through reusable dependencies
* ✅ ObjectId validation
* ✅ Owner-or-admin authorization
* ✅ Admin-only destructive operations
* ✅ Consistent success responses
* ✅ Proper HTTP status codes

---

# Next Lesson (One Complete File)

The next file will be the **complete updated**:

```text
app/services/skill_service.py
```

In that lesson we'll implement:

* User-specific duplicate validation
* Ownership authorization
* Admin override
* Remove `user_id` from requests
* `/skills/me`
* Production-grade authorization for all Skill APIs

This is one of the largest and most important files in the project.
