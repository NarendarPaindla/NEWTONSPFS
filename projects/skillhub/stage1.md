Excellent. We'll now build the **master project** that we'll use for the remainder of the course.

> **Version:** SkillHub API v1.0 (Production Style)

**Note:** I'm making one architectural improvement over our earlier lessons. We'll use a dedicated `core` package for configuration and a `lifespan` event instead of the older `startup`/`shutdown` events. This is the recommended approach in modern FastAPI.

---

# Part 1 — Project Foundation

---

# Step 1 — Create Project

```text
skillhub-api/
```

Open in VS Code.

---

# Step 2 — Create Virtual Environment

## Windows

```bash
python -m venv .venv
```

Activate

```bash
.venv\Scripts\activate
```

---

## Linux / macOS

```bash
python3 -m venv .venv

source .venv/bin/activate
```

---

# Step 3 — Install Packages

```bash
pip install fastapi
pip install uvicorn
pip install motor
pip install pymongo
pip install pydantic-settings
pip install python-dotenv
pip install email-validator
```

Freeze

```bash
pip freeze > requirements.txt
```

---

# Final Project Structure

```text
skillhub-api/
│
├── .env
├── requirements.txt
│
├── app/
│
│   ├── api/
│
│   ├── core/
│   │      config.py
│
│   ├── db/
│   │      database.py
│
│   ├── repositories/
│
│   ├── services/
│
│   ├── schemas/
│
│   ├── utils/
│
│   ├── exceptions/
│
│   └── main.py
│
└── .venv/
```

---

# File 1 — `.env`

```env
PROJECT_NAME=SkillHub API
PROJECT_VERSION=1.0.0
DEBUG=True

API_PREFIX=/api/v1

MONGODB_URL=mongodb://localhost:27017
DATABASE_NAME=skillhub_db
```

---

# Understanding

```env
PROJECT_NAME
```

Shown in Swagger UI.

---

```env
PROJECT_VERSION
```

API version.

---

```env
API_PREFIX
```

Later all APIs become

```text
/api/v1/users

/api/v1/skills
```

instead of

```text
/users

/skills
```

This makes future API versioning much easier.

---

```env
MONGODB_URL
```

Motor connection string.

---

```env
DATABASE_NAME
```

Database name.

---

# File 2 — `requirements.txt`

```text
fastapi
uvicorn
motor
pymongo
pydantic-settings
python-dotenv
email-validator
```

Later we'll add

```text
python-jose
passlib
bcrypt
python-multipart
```

during Authentication.

---

# File 3 — `app/core/config.py`

```python
from pydantic_settings import BaseSettings
from pydantic_settings import SettingsConfigDict


class Settings(BaseSettings):

    project_name: str

    project_version: str

    debug: bool

    api_prefix: str

    mongodb_url: str

    database_name: str

    model_config = SettingsConfigDict(

        env_file=".env",

        extra="ignore"

    )


settings = Settings()
```

---

# Why BaseSettings?

Instead of writing

```python
DATABASE_URL="..."
```

inside code,

we load values from

```text
.env
```

Advantages

* Secure
* Reusable
* Production Ready

---

# File 4 — `app/db/database.py`

```python
from motor.motor_asyncio import AsyncIOMotorClient

from app.core.config import settings


client = AsyncIOMotorClient(
    settings.mongodb_url
)

database = client[
    settings.database_name
]


async def connect_to_mongodb():

    await client.admin.command(
        "ping"
    )

    print(
        "✅ MongoDB Connected Successfully"
    )


async def close_mongodb_connection():

    client.close()

    print(
        "❌ MongoDB Connection Closed"
    )
```

---

# Why Ping?

```python
await client.admin.command(
    "ping"
)
```

Checks

```text
MongoDB Running?

YES

↓

Continue
```

Otherwise

Raises

Connection Error.

---

# File 5 — `app/main.py`

```python
from contextlib import asynccontextmanager

from fastapi import FastAPI
from fastapi import HTTPException

from app.core.config import settings

from app.db.database import (
    connect_to_mongodb,
    close_mongodb_connection
)

from app.exceptions.handlers import (
    http_exception_handler
)


@asynccontextmanager
async def lifespan(app: FastAPI):

    await connect_to_mongodb()

    yield

    await close_mongodb_connection()


app = FastAPI(

    title=settings.project_name,

    version=settings.project_version,

    debug=settings.debug,

    lifespan=lifespan

)


app.add_exception_handler(

    HTTPException,

    http_exception_handler

)


@app.get("/")
async def home():

    return {

        "message": f"Welcome to {settings.project_name}",

        "version": settings.project_version

    }


@app.get("/health")
async def health():

    return {

        "status": "Healthy",

        "database": "Connected"

    }


# Routers
# app.include_router(user_router)
# app.include_router(skill_router)
```

For now, the router registrations are commented out because those modules haven't been added yet. We'll uncomment them after creating the User and Skill modules.

---

# Why Lifespan?

Old FastAPI

```python
@app.on_event("startup")
```

Modern FastAPI

```python
lifespan()
```

It manages both startup and shutdown in one place.

---

# Current Request Flow

```text
Start Server

      │

      ▼

Lifespan

      │

      ▼

MongoDB Connected

      │

      ▼

FastAPI Running

      │

      ▼

Requests
```

---

# Run Server

```bash
uvicorn app.main:app --reload
```

Expected output

```text
✅ MongoDB Connected Successfully
```

---

# Open Swagger

```text
http://127.0.0.1:8000/docs
```

You should see

```text
SkillHub API

Version 1.0.0
```

---

# Test Root

```http
GET /
```

Response

```json
{
    "message":"Welcome to SkillHub API",
    "version":"1.0.0"
}
```

---

# Test Health

```http
GET /health
```

Response

```json
{
    "status":"Healthy",
    "database":"Connected"
}
```

---

# Architecture So Far

```text
               FastAPI

                   │

              Configuration

                   │

            Environment File

                   │

             MongoDB Connection

                   │

               Motor Client
```

---

# What We Completed

## Project

* ✅ Folder Structure
* ✅ Virtual Environment
* ✅ Dependencies

---

## Configuration

* ✅ `.env`
* ✅ Settings
* ✅ Environment Variables

---

## Database

* ✅ MongoDB Connection
* ✅ Motor Client
* ✅ Connection Check

---

## FastAPI

* ✅ Lifespan
* ✅ Root Endpoint
* ✅ Health Endpoint
* ✅ Exception Registration
* ✅ Ready for Router Registration

---

# Best Practices

* Never hardcode database credentials.
* Use `BaseSettings` with `.env` for configuration.
* Use FastAPI's `lifespan` instead of deprecated startup events.
* Keep database connection logic isolated in the `db` package.
* Add routers only after the corresponding modules are implemented.

---

# Next Part

We'll build the **shared infrastructure** used by both the User and Skill modules:

* `app/exceptions/handlers.py`
* `app/utils/object_id.py`
* `app/utils/responses.py`
* `app/utils/pagination.py`
* `app/utils/serializer.py`

These utilities will be reused throughout the rest of the project, reducing duplication and keeping the codebase clean.





Excellent. This part builds the **shared infrastructure** that every module (Users, Skills, Authentication, Projects, Reviews, etc.) will use.

> These files are written once and reused throughout the application.

---

# Part 2 — Common Utilities & Shared Components

---

# Updated Project Structure

```text
app/
│
├── api/
│
├── core/
│
├── db/
│
├── exceptions/
│      handlers.py
│
├── utils/
│      object_id.py
│      responses.py
│      pagination.py
│      serializer.py
│
├── repositories/
├── services/
├── schemas/
│
└── main.py
```

---

# File 1 — `app/exceptions/handlers.py`

```python
from fastapi import HTTPException
from fastapi import Request
from fastapi.responses import JSONResponse


async def http_exception_handler(
    request: Request,
    exc: HTTPException
):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "success": False,
            "message": exc.detail
        }
    )
```

---

## Why Global Exception Handling?

Without it

```python
raise HTTPException(
    status_code=404,
    detail="User Not Found"
)
```

returns

```json
{
    "detail": "User Not Found"
}
```

After registering our handler

```json
{
    "success": false,
    "message": "User Not Found"
}
```

Now **every API returns the same error format**.

---

# File 2 — `app/utils/object_id.py`

```python
from bson import ObjectId


def validate_object_id(id: str) -> ObjectId | None:

    if not ObjectId.is_valid(id):
        return None

    return ObjectId(id)
```

---

## Why?

Clients send

```text
6872abcd...
```

MongoDB stores

```python
ObjectId(...)
```

We convert safely.

---

## Example

Valid

```python
validate_object_id(
    "6872abc..."
)
```

↓

Returns

```python
ObjectId(...)
```

---

Invalid

```python
validate_object_id("abc")
```

↓

Returns

```python
None
```

---

# File 3 — `app/utils/responses.py`

```python
def success_response(
    message: str,
    data=None
):

    return {

        "success": True,

        "message": message,

        "data": data

    }
```

---

## Instead of

```python
return {
    ...
}
```

Every API becomes

```python
return success_response(
    "User Created",
    data
)
```

Much cleaner.

---

# Example Response

```json
{
    "success": true,
    "message": "User Created Successfully",
    "data": {
        "id": "6872..."
    }
}
```

---

# File 4 — `app/utils/pagination.py`

```python
def paginated_response(
    message: str,
    data,
    page: int,
    limit: int,
    total_records: int,
    total_pages: int,
    has_next: bool,
    has_previous: bool
):

    return {

        "success": True,

        "message": message,

        "data": data,

        "pagination": {

            "page": page,

            "limit": limit,

            "total_records": total_records,

            "total_pages": total_pages,

            "current_count": len(data),

            "has_next": has_next,

            "has_previous": has_previous

        }

    }
```

---

## Example

```json
{
    "success": true,
    "message": "Skills fetched successfully",

    "data": [

    ],

    "pagination": {

        "page": 2,

        "limit": 5,

        "total_records": 42,

        "total_pages": 9,

        "current_count": 5,

        "has_next": true,

        "has_previous": true

    }
}
```

---

# Why Separate File?

Soon we'll have

* Skills
* Users
* Projects
* Reviews
* Comments

Every list API can use

```python
paginated_response()
```

No duplicated code.

---

# File 5 — `app/utils/serializer.py`

We'll place every serializer here.

Later we may split them into separate files if the project becomes very large.

---

## Skill Serializer

```python
def skill_serializer(
    skill
):

    return {

        "id": str(skill["_id"]),

        "name": skill["name"],

        "category": skill["category"],

        "level": skill["level"],

        "experience": skill["experience"],

        "user_id": str(
            skill["user_id"]
        )

    }
```

---

## Skill List Serializer

```python
def skill_serializer_list(
    skills
):

    return [

        skill_serializer(skill)

        for skill in skills

    ]
```

---

## User Serializer

```python
def user_serializer(
    user
):

    return {

        "id": str(user["_id"]),

        "full_name": user["full_name"],

        "username": user["username"],

        "email": user["email"],

        "bio": user.get("bio"),

        "location": user.get("location")

    }
```

---

## User List Serializer

```python
def user_serializer_list(
    users
):

    return [

        user_serializer(user)

        for user in users

    ]
```

---

# Why Serializer?

MongoDB returns

```python
{
    "_id": ObjectId(...)
}
```

Frontend expects

```json
{
    "id":"6872..."
}
```

Serializer converts

MongoDB

↓

Frontend

---

# Visual

```text
MongoDB Document

        │

        ▼

Serializer

        │

        ▼

JSON Response
```

---

# Reusable Utilities

Now every module can use

```python
validate_object_id()
```

---

```python
success_response()
```

---

```python
paginated_response()
```

---

```python
skill_serializer()
```

---

```python
user_serializer()
```

No duplication.

---

# Current Architecture

```text
              FastAPI

                 │

            HTTP Request

                 │

              Router

                 │

         Shared Utilities

                 │

             Service Layer

                 │

          Repository Layer

                 │

              MongoDB
```

---

# What We Completed

## Exception Handling

* ✅ Global HTTP Exception Handler

---

## Utilities

* ✅ ObjectId Validation
* ✅ Success Response
* ✅ Pagination Response

---

## Serialization

* ✅ Skill Serializer
* ✅ Skill List Serializer
* ✅ User Serializer
* ✅ User List Serializer

---

# Best Practices

* Keep serializers separate from business logic.
* Never return raw MongoDB `ObjectId` values.
* Use helper functions instead of repeating response structures.
* Centralize common utilities so every module behaves consistently.

---

# Small Improvement for Later

As the project grows (Users, Skills, Reviews, Comments, Projects, Authentication), a single `serializer.py` file will become too large.

In a production project, we would eventually refactor it into:

```text
app/
└── utils/
    ├── serializers/
    │   ├── skill_serializer.py
    │   ├── user_serializer.py
    │   ├── review_serializer.py
    │   └── comment_serializer.py
    ├── object_id.py
    ├── pagination.py
    └── responses.py
```

For now, keeping them together makes the project easier to follow as you're learning.

---

# Next Part

We'll build the **complete User Module**, fully synchronized with this foundation:

* `user_schema.py`
* `user_repository.py`
* `user_service.py`
* `users.py`

This version will be the one we'll continue using when we implement **JWT Authentication** later in the course.


Excellent. From this point onward, we'll build the **User Module** on top of the project foundation we already created.

**Goal:** Build a complete production-style User module that will later support JWT Authentication.

---

# Part 3 — User Module

---

# Final Folder Structure

```text
app/
│
├── api/
│     users.py
│
├── repositories/
│     user_repository.py
│
├── schemas/
│     user_schema.py
│
├── services/
│     user_service.py
│
└── utils/
      serializer.py
```

---

# File 1 — `app/schemas/user_schema.py`

```python
from pydantic import BaseModel
from pydantic import EmailStr
from pydantic import Field


class UserCreate(BaseModel):

    full_name: str = Field(
        ...,
        min_length=2,
        max_length=100
    )

    username: str = Field(
        ...,
        min_length=3,
        max_length=30
    )

    email: EmailStr

    bio: str | None = None

    location: str | None = None


class UserUpdate(BaseModel):

    full_name: str = Field(
        ...,
        min_length=2,
        max_length=100
    )

    username: str = Field(
        ...,
        min_length=3,
        max_length=30
    )

    email: EmailStr

    bio: str | None = None

    location: str | None = None


class UserResponse(BaseModel):

    id: str

    full_name: str

    username: str

    email: EmailStr

    bio: str | None = None

    location: str | None = None
```

---

# Why Three Schemas?

## UserCreate

Used in

```http
POST /users
```

---

## UserUpdate

Used in

```http
PUT /users/{id}
```

---

## UserResponse

Returned to frontend.

This keeps request and response models independent.

---

# File 2 — `app/repositories/user_repository.py`

```python
from bson import ObjectId

from app.db.database import database


class UserRepository:

    async def create(
        self,
        data: dict
    ):
        return await database.users.insert_one(
            data
        )

    async def get_all(self):
        return await (
            database.users
            .find()
            .to_list(length=None)
        )

    async def get_by_id(
        self,
        object_id: ObjectId
    ):
        return await database.users.find_one(
            {
                "_id": object_id
            }
        )

    async def get_by_email(
        self,
        email: str
    ):
        return await database.users.find_one(
            {
                "email": email
            }
        )

    async def get_by_username(
        self,
        username: str
    ):
        return await database.users.find_one(
            {
                "username": username
            }
        )

    async def update(
        self,
        object_id: ObjectId,
        data: dict
    ):
        return await database.users.update_one(
            {
                "_id": object_id
            },
            {
                "$set": data
            }
        )

    async def delete(
        self,
        object_id: ObjectId
    ):
        return await database.users.delete_one(
            {
                "_id": object_id
            }
        )


user_repository = UserRepository()
```

---

# Repository Responsibility

Repository only talks to MongoDB.

No

* HTTPException
* Validation
* Business Logic

---

# File 3 — `app/services/user_service.py`

```python
from fastapi import HTTPException

from app.repositories.user_repository import (
    user_repository
)


class UserService:

    async def create_user(
        self,
        user
    ):
        existing_email = await (
            user_repository.get_by_email(
                user.email
            )
        )

        if existing_email:
            raise HTTPException(
                status_code=400,
                detail="Email already exists"
            )

        existing_username = await (
            user_repository.get_by_username(
                user.username
            )
        )

        if existing_username:
            raise HTTPException(
                status_code=400,
                detail="Username already exists"
            )

        return await user_repository.create(
            user.model_dump()
        )

    async def get_users(self):
        return await user_repository.get_all()

    async def get_user(
        self,
        object_id
    ):
        user = await (
            user_repository.get_by_id(
                object_id
            )
        )

        if user is None:
            raise HTTPException(
                status_code=404,
                detail="User Not Found"
            )

        return user

    async def update_user(
        self,
        object_id,
        user
    ):
        result = await user_repository.update(
            object_id,
            user.model_dump()
        )

        if result.matched_count == 0:
            raise HTTPException(
                status_code=404,
                detail="User Not Found"
            )

        return await user_repository.get_by_id(
            object_id
        )

    async def delete_user(
        self,
        object_id
    ):
        result = await user_repository.delete(
            object_id
        )

        if result.deleted_count == 0:
            raise HTTPException(
                status_code=404,
                detail="User Not Found"
            )


user_service = UserService()
```

---

# Service Responsibilities

Business Logic

✔ Duplicate Email

✔ Duplicate Username

✔ User Exists

Repository doesn't know any of this.

---

# File 4 — `app/api/users.py`

```python
from fastapi import APIRouter
from fastapi import HTTPException
from fastapi import status

from app.schemas.user_schema import (
    UserCreate,
    UserUpdate
)

from app.services.user_service import (
    user_service
)

from app.utils.object_id import (
    validate_object_id
)

from app.utils.responses import (
    success_response
)

from app.utils.serializer import (
    user_serializer,
    user_serializer_list
)

from app.core.config import settings


router = APIRouter(

    prefix=f"{settings.api_prefix}/users",

    tags=["Users"]

)
```

---

## Create User

```python
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

        "User Created Successfully",

        {

            "id": str(result.inserted_id)

        }

    )
```

---

## Get All Users

```python
@router.get("/")
async def get_users():

    users = await user_service.get_users()

    return success_response(

        "Users fetched successfully",

        user_serializer_list(users)

    )
```

---

## Get User By ID

```python
@router.get("/{user_id}")
async def get_user(
    user_id: str
):

    object_id = validate_object_id(
        user_id
    )

    if object_id is None:
        raise HTTPException(
            status_code=400,
            detail="Invalid User ID"
        )

    user = await user_service.get_user(
        object_id
    )

    return success_response(

        "User fetched successfully",

        user_serializer(user)

    )
```

---

## Update User

```python
@router.put("/{user_id}")
async def update_user(
    user_id: str,
    user: UserUpdate
):

    object_id = validate_object_id(
        user_id
    )

    if object_id is None:
        raise HTTPException(
            status_code=400,
            detail="Invalid User ID"
        )

    updated_user = await user_service.update_user(
        object_id,
        user
    )

    return success_response(

        "User Updated Successfully",

        user_serializer(updated_user)

    )
```

---

## Delete User

```python
@router.delete(
    "/{user_id}",
    status_code=status.HTTP_204_NO_CONTENT
)
async def delete_user(
    user_id: str
):

    object_id = validate_object_id(
        user_id
    )

    if object_id is None:
        raise HTTPException(
            status_code=400,
            detail="Invalid User ID"
        )

    await user_service.delete_user(
        object_id
    )
```

---

# Update `main.py`

Now uncomment the router registration.

```python
from app.api.users import router as user_router

app.include_router(user_router)
```

---

# Swagger APIs

```
POST   /api/v1/users

GET    /api/v1/users

GET    /api/v1/users/{id}

PUT    /api/v1/users/{id}

DELETE /api/v1/users/{id}
```

---

# Sample Request

```json
{
    "full_name": "Paindla Narendar Reddy",
    "username": "narendar",
    "email": "narendar@gmail.com",
    "bio": "Python Backend Trainer",
    "location": "Hyderabad"
}
```

---

# MongoDB Document

```json
{
    "_id": ObjectId("6872..."),

    "full_name": "Paindla Narendar Reddy",

    "username": "narendar",

    "email": "narendar@gmail.com",

    "bio": "Python Backend Trainer",

    "location": "Hyderabad"
}
```

---

# What We Built

## Users Module

✅ Schemas

✅ Repository

✅ Service

✅ Router

✅ CRUD APIs

✅ Duplicate Email Validation

✅ Duplicate Username Validation

✅ ObjectId Validation

✅ Standard Responses

---

# One Improvement We'll Make Later

Notice that `update_user()` currently doesn't check whether the **updated email** or **updated username** already belongs to another user.

When we implement authentication and more advanced validation, we'll enhance this by:

* Checking duplicates while excluding the current user.
* Adding MongoDB **unique indexes** so the database also enforces uniqueness.

This gives us both application-level and database-level protection.

---

# Next Part

We'll build the **complete Skills Module**, synchronized with this new project foundation.

It will include:

* Complete CRUD
* Pagination
* Filtering
* Searching
* Sorting
* User relationships (`user_id`)
* Production-ready router, service, and repository

This Skills module will become the foundation for the upcoming JWT Authentication and MongoDB Aggregation lessons.

Excellent. This is the **final Skill Module** that we'll use throughout the remaining course (JWT, Authorization, Aggregation, Docker, Deployment, etc.).

This version is cleaner than our earlier implementation and is fully synchronized with the latest project architecture.

---

# Part 4A — Skill Schema & Repository

---

# Project Structure

```text
app/
│
├── schemas/
│      skill_schema.py
│
├── repositories/
│      skill_repository.py
│
├── services/
│      skill_service.py
│
└── api/
       skills.py
```

---

# File 1 — `app/schemas/skill_schema.py`

```python
from pydantic import BaseModel
from pydantic import Field


class SkillBase(BaseModel):
    name: str = Field(
        ...,
        min_length=2,
        max_length=100,
        description="Skill name"
    )

    category: str = Field(
        ...,
        min_length=2,
        max_length=50,
        description="Skill category"
    )

    level: str = Field(
        ...,
        min_length=2,
        max_length=30,
        description="Skill level"
    )

    experience: int = Field(
        ...,
        ge=0,
        le=50,
        description="Years of experience"
    )


class SkillCreate(SkillBase):
    user_id: str


class SkillUpdate(SkillBase):
    user_id: str


class SkillResponse(SkillBase):
    id: str
    user_id: str
```

---

# Why Create `SkillBase`?

Instead of writing

```python
name

category

level

experience
```

three times

We write once.

Then inherit.

```text
SkillBase
      ▲
      │
 ┌────┴─────┐
 │          │
Create   Update
      ▲
      │
Response
```

Much cleaner.

---

# Validation

## Name

```python
Field(
    ...,
    min_length=2,
    max_length=100
)
```

Valid

```text
Python
```

Invalid

```text
P
```

---

## Experience

```python
ge=0

le=50
```

Valid

```text
5
```

Invalid

```text
-10
```

---

# Example Request

```json
{
    "name":"Python",

    "category":"Backend",

    "level":"Advanced",

    "experience":4,

    "user_id":"6872abcd1234567890123456"
}
```

---

# File 2 — `app/repositories/skill_repository.py`

```python
from bson import ObjectId

from app.db.database import database


class SkillRepository:

    async def create(
        self,
        data: dict
    ):
        return await database.skills.insert_one(
            data
        )

    async def get_all(
        self,
        query: dict,
        skip: int,
        limit: int,
        sort_field: str,
        sort_order: int
    ):
        return await (
            database.skills
            .find(query)
            .sort(sort_field, sort_order)
            .skip(skip)
            .limit(limit)
            .to_list(length=limit)
        )

    async def count(
        self,
        query: dict
    ):
        return await database.skills.count_documents(
            query
        )

    async def get_by_id(
        self,
        object_id: ObjectId
    ):
        return await database.skills.find_one(
            {
                "_id": object_id
            }
        )

    async def get_by_name(
        self,
        name: str
    ):
        return await database.skills.find_one(
            {
                "name": name
            }
        )

    async def get_by_user(
        self,
        object_id: ObjectId
    ):
        return await (
            database.skills
            .find(
                {
                    "user_id": object_id
                }
            )
            .to_list(length=None)
        )

    async def count_by_user(
        self,
        object_id: ObjectId
    ):
        return await database.skills.count_documents(
            {
                "user_id": object_id
            }
        )

    async def update(
        self,
        object_id: ObjectId,
        data: dict
    ):
        return await database.skills.update_one(
            {
                "_id": object_id
            },
            {
                "$set": data
            }
        )

    async def delete(
        self,
        object_id: ObjectId
    ):
        return await database.skills.delete_one(
            {
                "_id": object_id
            }
        )

    # ----------------------------
    # Aggregation APIs
    # ----------------------------

    async def get_backend_skills(
        self
    ):
        cursor = database.skills.aggregate(
            [
                {
                    "$match": {
                        "category": "Backend"
                    }
                }
            ]
        )

        return await cursor.to_list(
            length=None
        )

    async def backend_skill_summary(
        self
    ):
        cursor = database.skills.aggregate(
            [
                {
                    "$match": {
                        "category": "Backend"
                    }
                },
                {
                    "$project": {
                        "_id": 0,
                        "name": 1,
                        "experience": 1
                    }
                }
            ]
        )

        return await cursor.to_list(
            length=None
        )


skill_repository = SkillRepository()
```

---

# Repository Responsibilities

The repository only knows MongoDB.

It does **NOT**

* Raise `HTTPException`
* Calculate pagination
* Validate duplicate skills
* Validate users
* Build API responses

Those belong elsewhere.

---

# CRUD Methods

## Create

```python
insert_one()
```

Used by

```http
POST /skills
```

---

## Get All

Supports

* Pagination
* Filtering
* Searching
* Sorting

through

```python
query

skip

limit

sort_field

sort_order
```

One reusable method.

---

## Count

Used for

```text
Pagination Metadata
```

Returns

```text
Total Records
```

---

## Get By User

```python
.find(
{
"user_id":object_id
}
)
```

Returns

All skills owned by one user.

---

## Count By User

```python
count_documents()
```

Used for

```http
GET /skills/user/{user_id}/count
```

---

## Aggregation

Already included.

Today we support

```python
$match
```

and

```python
$project
```

Later we'll add

* `$lookup`
* `$group`
* `$unwind`
* `$facet`

---

# Current Database

## Users

```json
{
    "_id": ObjectId("U1"),

    "full_name": "Narendar"
}
```

---

## Skills

```json
{
    "_id": ObjectId(),

    "name": "Python",

    "category": "Backend",

    "level": "Advanced",

    "experience": 4,

    "user_id": ObjectId("U1")
}
```

Relationship

```text
User

↓

Many Skills
```

---

# Repository Architecture

```text
Service

↓

Repository

↓

MongoDB
```

Never

```text
Router

↓

MongoDB
```

---

# Best Practices

* Keep repositories database-focused.
* Don't raise HTTP exceptions in repositories.
* Reuse query methods instead of writing duplicate MongoDB logic.
* Keep aggregation pipelines inside the repository because they are database operations.

---

# One Improvement We'll Make Later

Notice `get_by_name()` checks only the skill name.

When JWT Authentication is added, we'll change this to:

```python
get_by_name_and_user(
    name,
    user_id
)
```

so that:

```text
User A
Python ✅

User B
Python ✅

User A
Python
Python ❌
```

This is the behavior used by most multi-user applications.

---

# Next Part (4B)

We'll build the **complete `SkillService`**, including:

* User validation
* Duplicate skill validation
* Dynamic query building
* Pagination calculations
* Search
* Filtering
* Sorting
* Aggregation methods
* Business rules

This is the most important service layer in the entire project.

Excellent. This is the **brain of the Skill Module**.

The **Service Layer** is responsible for all business logic.

Remember the architecture:

```text
Client
   │
   ▼
Router (HTTP)
   │
   ▼
Service (Business Logic)
   │
   ▼
Repository (Database)
   │
   ▼
MongoDB
```

The service layer decides **what** should happen; the repository decides **how** to communicate with MongoDB.

---

# Part 4B — `app/services/skill_service.py`

```python
import math

from fastapi import HTTPException

from app.repositories.skill_repository import (
    skill_repository
)

from app.repositories.user_repository import (
    user_repository
)

from app.utils.object_id import (
    validate_object_id
)


class SkillService:

    async def create_skill(
        self,
        skill
    ):
        # Validate User ID
        object_id = validate_object_id(
            skill.user_id
        )

        if object_id is None:
            raise HTTPException(
                status_code=400,
                detail="Invalid User ID"
            )

        # Check User Exists
        user = await user_repository.get_by_id(
            object_id
        )

        if user is None:
            raise HTTPException(
                status_code=404,
                detail="User Not Found"
            )

        # Check Duplicate Skill
        existing_skill = await (
            skill_repository.get_by_name(
                skill.name
            )
        )

        if existing_skill:
            raise HTTPException(
                status_code=400,
                detail="Skill Already Exists"
            )

        # Convert Request Model → Dictionary
        data = skill.model_dump()

        # Convert user_id string → ObjectId
        data["user_id"] = object_id

        return await skill_repository.create(
            data
        )

    async def get_skill(
        self,
        object_id
    ):
        skill = await skill_repository.get_by_id(
            object_id
        )

        if skill is None:
            raise HTTPException(
                status_code=404,
                detail="Skill Not Found"
            )

        return skill

    async def get_all_skills(
        self,
        page: int,
        limit: int,
        category: str | None,
        level: str | None,
        search: str | None,
        sort: str,
        order: str
    ):
        query = {}

        if category:
            query["category"] = category

        if level:
            query["level"] = level

        if search:
            query["name"] = {
                "$regex": search,
                "$options": "i"
            }

        skip = (page - 1) * limit

        allowed_sort_fields = {
            "name",
            "category",
            "level",
            "experience"
        }

        if sort not in allowed_sort_fields:
            sort = "name"

        sort_order = (
            -1
            if order.lower() == "desc"
            else 1
        )

        skills = await skill_repository.get_all(
            query=query,
            skip=skip,
            limit=limit,
            sort_field=sort,
            sort_order=sort_order
        )

        total_records = await (
            skill_repository.count(query)
        )

        total_pages = (
            math.ceil(total_records / limit)
            if total_records
            else 1
        )

        return {

            "skills": skills,

            "page": page,

            "limit": limit,

            "total_records": total_records,

            "total_pages": total_pages,

            "has_next": page < total_pages,

            "has_previous": page > 1

        }

    async def update_skill(
        self,
        object_id,
        skill
    ):
        user_object_id = validate_object_id(
            skill.user_id
        )

        if user_object_id is None:
            raise HTTPException(
                status_code=400,
                detail="Invalid User ID"
            )

        user = await user_repository.get_by_id(
            user_object_id
        )

        if user is None:
            raise HTTPException(
                status_code=404,
                detail="User Not Found"
            )

        data = skill.model_dump()

        data["user_id"] = user_object_id

        result = await skill_repository.update(
            object_id,
            data
        )

        if result.matched_count == 0:
            raise HTTPException(
                status_code=404,
                detail="Skill Not Found"
            )

        return await skill_repository.get_by_id(
            object_id
        )

    async def delete_skill(
        self,
        object_id
    ):
        result = await skill_repository.delete(
            object_id
        )

        if result.deleted_count == 0:
            raise HTTPException(
                status_code=404,
                detail="Skill Not Found"
            )

    async def get_user_skills(
        self,
        user_id: str
    ):
        object_id = validate_object_id(
            user_id
        )

        if object_id is None:
            raise HTTPException(
                status_code=400,
                detail="Invalid User ID"
            )

        user = await user_repository.get_by_id(
            object_id
        )

        if user is None:
            raise HTTPException(
                status_code=404,
                detail="User Not Found"
            )

        return await skill_repository.get_by_user(
            object_id
        )

    async def get_user_skill_count(
        self,
        user_id: str
    ):
        object_id = validate_object_id(
            user_id
        )

        if object_id is None:
            raise HTTPException(
                status_code=400,
                detail="Invalid User ID"
            )

        return await skill_repository.count_by_user(
            object_id
        )

    async def get_backend_skills(
        self
    ):
        return await (
            skill_repository.get_backend_skills()
        )

    async def backend_skill_summary(
        self
    ):
        return await (
            skill_repository.backend_skill_summary()
        )


skill_service = SkillService()
```

---

# Service Responsibilities

## 1. User Validation

```python
validate_object_id()
```

↓

Checks

* Valid MongoDB ObjectId
* User Exists

---

## 2. Duplicate Skill Validation

```python
get_by_name()
```

Current rule

```text
Python

Python ❌
```

Later

```text
Python (User A)

Python (User B)

✔ Allowed
```

---

## 3. Dynamic Query

```python
query={}
```

Then

```python
query["category"]
```

Only added if client provides

```http
?category=Backend
```

---

## 4. Search

```python
"$regex"
```

Allows

```text
Py

↓

Python
```

Case insensitive.

---

## 5. Pagination

```python
skip=(page-1)*limit
```

Industry standard.

---

## 6. Sorting

Allowed

```text
name

category

level

experience
```

Invalid field?

↓

Automatically

```text
name
```

---

## 7. Pagination Metadata

Returns

```text
Total Records

Total Pages

Has Next

Has Previous
```

Frontend can build pagination easily.

---

## 8. Aggregation

Service exposes

```python
get_backend_skills()
```

and

```python
backend_skill_summary()
```

Repository handles MongoDB pipeline.

Service only delegates.

---

# Architecture

```text
Router

↓

Service

├── User Validation

├── Duplicate Check

├── Search

├── Pagination

├── Sorting

├── Filtering

├── Aggregation

↓

Repository

↓

MongoDB
```

---

# Best Practices

* Keep all business rules in the Service Layer.
* Never write MongoDB queries in the Router.
* Keep repositories free of HTTP-specific code.
* Validate related entities before creating or updating child records.
* Restrict sortable fields to a predefined allow-list.

---

# Planned Improvements

When we implement authentication, we'll refine this service by:

* Using the logged-in user's ID instead of accepting `user_id` from the request.
* Checking duplicate skills per user (`user_id + skill_name`).
* Adding audit fields (`created_at`, `updated_at`, `created_by`).
* Moving repeated validation into reusable helper methods.

These changes build on the service you've written here without changing the overall architecture.

---

# Next Part (4C)

We'll build the **complete `app/api/skills.py` router**, including:

* ✅ Create Skill
* ✅ Get All Skills
* ✅ Get Skill By ID
* ✅ Update Skill
* ✅ Delete Skill
* ✅ Get Skills By User
* ✅ Get User Skill Count
* ✅ Backend Skills Aggregation API
* ✅ Backend Skill Summary API

After that, you'll have a complete, synchronized SkillHub API that serves as the foundation for **JWT Authentication** and advanced **MongoDB Aggregation (`$lookup`, `$group`, `$unwind`)**.

Excellent. This completes the **SkillHub v1.0** project.

After this lesson, your project will have:

* ✅ Complete Users Module
* ✅ Complete Skills Module
* ✅ CRUD
* ✅ Pagination
* ✅ Filtering
* ✅ Searching
* ✅ Sorting
* ✅ MongoDB Aggregation (`$match`, `$project`)
* ✅ Repository Pattern
* ✅ Service Layer
* ✅ Production Architecture

---

# Part 4C — `app/api/skills.py`

---

## Complete Imports

```python
from typing import Annotated

from fastapi import APIRouter
from fastapi import HTTPException
from fastapi import Query
from fastapi import status

from app.core.config import settings

from app.schemas.skill_schema import (
    SkillCreate,
    SkillUpdate
)

from app.services.skill_service import (
    skill_service
)

from app.utils.object_id import (
    validate_object_id
)

from app.utils.responses import (
    success_response
)

from app.utils.pagination import (
    paginated_response
)

from app.utils.serializer import (
    skill_serializer,
    skill_serializer_list
)
```

---

## Router

```python
router = APIRouter(
    prefix=f"{settings.api_prefix}/skills",
    tags=["Skills"]
)
```

---

# Create Skill

```python
@router.post(
    "/",
    status_code=status.HTTP_201_CREATED
)
async def create_skill(
    skill: SkillCreate
):

    result = await skill_service.create_skill(
        skill
    )

    return success_response(
        "Skill Created Successfully",
        {
            "id": str(result.inserted_id)
        }
    )
```

---

# Get All Skills

```python
@router.get("/")
async def get_skills(

    page: Annotated[
        int,
        Query(ge=1)
    ] = 1,

    limit: Annotated[
        int,
        Query(
            ge=1,
            le=100
        )
    ] = 10,

    category: str | None = None,

    level: str | None = None,

    search: str | None = None,

    sort: str = "name",

    order: str = "asc"

):

    result = await skill_service.get_all_skills(

        page=page,

        limit=limit,

        category=category,

        level=level,

        search=search,

        sort=sort,

        order=order

    )

    return paginated_response(

        message="Skills fetched successfully",

        data=skill_serializer_list(
            result["skills"]
        ),

        page=result["page"],

        limit=result["limit"],

        total_records=result["total_records"],

        total_pages=result["total_pages"],

        has_next=result["has_next"],

        has_previous=result["has_previous"]

    )
```

---

# Get Skill By ID

```python
@router.get("/{skill_id}")
async def get_skill(
    skill_id: str
):

    object_id = validate_object_id(
        skill_id
    )

    if object_id is None:
        raise HTTPException(
            status_code=400,
            detail="Invalid Skill ID"
        )

    skill = await skill_service.get_skill(
        object_id
    )

    return success_response(
        "Skill fetched successfully",
        skill_serializer(skill)
    )
```

---

# Update Skill

```python
@router.put("/{skill_id}")
async def update_skill(
    skill_id: str,
    skill: SkillUpdate
):

    object_id = validate_object_id(
        skill_id
    )

    if object_id is None:
        raise HTTPException(
            status_code=400,
            detail="Invalid Skill ID"
        )

    updated_skill = await skill_service.update_skill(
        object_id,
        skill
    )

    return success_response(
        "Skill Updated Successfully",
        skill_serializer(updated_skill)
    )
```

---

# Delete Skill

```python
@router.delete(
    "/{skill_id}",
    status_code=status.HTTP_204_NO_CONTENT
)
async def delete_skill(
    skill_id: str
):

    object_id = validate_object_id(
        skill_id
    )

    if object_id is None:
        raise HTTPException(
            status_code=400,
            detail="Invalid Skill ID"
        )

    await skill_service.delete_skill(
        object_id
    )
```

---

# Get Skills By User

```python
@router.get("/user/{user_id}")
async def get_user_skills(
    user_id: str
):

    skills = await skill_service.get_user_skills(
        user_id
    )

    return success_response(
        "User Skills fetched successfully",
        skill_serializer_list(skills)
    )
```

---

# Get User Skill Count

A cleaner route than our earlier version is:

```python
@router.get("/user/{user_id}/count")
async def get_user_skill_count(
    user_id: str
):

    total = await skill_service.get_user_skill_count(
        user_id
    )

    return success_response(
        "Skill count fetched successfully",
        {
            "user_id": user_id,
            "total_skills": total
        }
    )
```

This produces:

```http
GET /api/v1/skills/user/{user_id}/count
```

which is more RESTful and avoids repeating `/users/.../skills`.

---

# Aggregation API

## Backend Skills

```python
@router.get("/category/backend")
async def backend_skills():

    skills = await skill_service.get_backend_skills()

    return success_response(
        "Backend Skills",
        skill_serializer_list(skills)
    )
```

---

## Backend Summary

```python
@router.get("/category/backend/summary")
async def backend_summary():

    data = await skill_service.backend_skill_summary()

    return success_response(
        "Backend Skill Summary",
        data
    )
```

---

# Update `main.py`

Register the Skills router.

```python
from app.api.skills import router as skill_router

app.include_router(skill_router)
```

Your router registrations now look like:

```python
from app.api.users import router as user_router
from app.api.skills import router as skill_router

app.include_router(user_router)
app.include_router(skill_router)
```

---

# Final Swagger

```
POST

/api/v1/users
```

```
GET

/api/v1/users
```

```
GET

/api/v1/users/{id}
```

```
PUT

/api/v1/users/{id}
```

```
DELETE

/api/v1/users/{id}
```

---

```
POST

/api/v1/skills
```

```
GET

/api/v1/skills
```

Supports

* Pagination

* Search

* Filtering

* Sorting

---

```
GET

/api/v1/skills/{id}
```

---

```
PUT

/api/v1/skills/{id}
```

---

```
DELETE

/api/v1/skills/{id}
```

---

```
GET

/api/v1/skills/user/{user_id}
```

---

```
GET

/api/v1/skills/user/{user_id}/count
```

---

```
GET

/api/v1/skills/category/backend
```

---

```
GET

/api/v1/skills/category/backend/summary
```

---

# Final Architecture

```text
Client

        │

        ▼

Router

        │

        ▼

Service

        │

        ▼

Repository

        │

        ▼

MongoDB
```

---

# Current Project Structure

```text
skillhub-api/

│

├── app/

│   ├── api/
│   │     users.py
│   │     skills.py

│   ├── core/
│   │     config.py

│   ├── db/
│   │     database.py

│   ├── exceptions/
│   │     handlers.py

│   ├── repositories/
│   │     user_repository.py
│   │     skill_repository.py

│   ├── services/
│   │     user_service.py
│   │     skill_service.py

│   ├── schemas/
│   │     user_schema.py
│   │     skill_schema.py

│   ├── utils/
│   │     object_id.py
│   │     responses.py
│   │     pagination.py
│   │     serializer.py

│   └── main.py

│

├── .env

├── requirements.txt
```

---

# Improvements We'll Make in Later Modules

Before moving to JWT Authentication, there are a few refinements we'll introduce:

* Add `response_model` definitions for better OpenAPI documentation.
* Create generic response schemas (`SuccessResponse`, `PaginatedResponse`) instead of returning plain dictionaries.
* Add MongoDB indexes (unique email, username, and optimized search fields).
* Introduce audit fields (`created_at`, `updated_at`).
* Move repeated validation into reusable dependencies and helper functions.

These enhancements will make the project even closer to production standards while preserving the layered architecture you've built.

---

# 🚀 Next Module

Now that SkillHub v1.0 is complete, we'll begin **Module 5 — Authentication**.

We'll implement:

* Password hashing with **Passlib**
* **bcrypt**
* User Registration with password storage
* User Login
* JWT Access Tokens
* Refresh Tokens
* `OAuth2PasswordBearer`
* Protected APIs
* Current User
* Logout
* Token Expiry
* Secure ownership of skills (removing the need to send `user_id` in requests)

This is the point where the project evolves from a public CRUD API into a secure, production-style backend.







