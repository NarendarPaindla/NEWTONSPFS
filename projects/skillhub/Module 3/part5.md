# Backend Development with FastAPI + MongoDB

# **Module 3 — Part 5**

# **Repository Pattern + Service Layer (Industry Architecture)**

> ⭐ **This lesson changes how you think about backend development.**
>
> Up to now, our code works.
>
> But it is **not scalable**.
>
> Today we'll refactor our project into the architecture used in many production FastAPI applications.

---

# 1. Learning Objectives

After this lesson, you will understand:

* Why large projects don't put database code inside API routes.
* What is the Repository Pattern?
* What is the Service Layer?
* Separation of Concerns (SoC).
* How requests flow through different layers.
* How to build maintainable applications.

---

# 2. The Current Problem

Our current API looks like this:

```python
@router.post("/skills")
async def create_skill(skill: SkillCreate):
    result = await database.skills.insert_one(skill.model_dump())
    return success_response(...)
```

At first, this looks fine.

But imagine after six months, your `skills.py` contains:

* 20 APIs
* Database queries
* Validation
* Business rules
* Logging
* Notifications
* Analytics

One file becomes 800+ lines long.

This is difficult to read, test, and maintain.

---

# 3. Separation of Responsibilities

Instead of putting everything in one place, we divide responsibilities.

```text
Client
   │
   ▼
API Layer
   │
   ▼
Service Layer
   │
   ▼
Repository Layer
   │
   ▼
MongoDB
```

Each layer has exactly **one responsibility**.

---

# 4. What Does Each Layer Do?

## API Layer

Responsible for:

* Receiving HTTP requests.
* Validating input.
* Returning HTTP responses.

It **should not** contain database queries.

---

## Service Layer

Responsible for:

* Business logic.
* Rules.
* Calculations.
* Decision making.

Example:

> "A user cannot add duplicate skills."

This rule belongs in the Service Layer.

---

## Repository Layer

Responsible only for database operations.

Examples:

* Insert document.
* Find document.
* Update document.
* Delete document.

Nothing else.

---

# 5. Folder Structure

Create two new folders (if not already created):

```text
app/
│
├── repositories/
│   └── skill_repository.py
│
├── services/
│   └── skill_service.py
```

---

# 6. Repository Layer

**File:** `app/repositories/skill_repository.py`

```python
from app.db.database import database


class SkillRepository:

    async def create(self, skill_data: dict):
        return await database.skills.insert_one(skill_data)

    async def get_all(self):
        return await database.skills.find().to_list(length=None)

    async def get_by_id(self, object_id):
        return await database.skills.find_one(
            {"_id": object_id}
        )

    async def update(self, object_id, data):
        return await database.skills.update_one(
            {"_id": object_id},
            {"$set": data}
        )

    async def delete(self, object_id):
        return await database.skills.delete_one(
            {"_id": object_id}
        )


skill_repository = SkillRepository()
```

---

# 7. Why Create a Class?

Today we have:

```text
SkillRepository
```

Tomorrow we'll have:

```text
UserRepository

CommentRepository

BookmarkRepository

ProjectRepository

CategoryRepository
```

Each repository manages one collection.

This keeps responsibilities clear.

---

# 8. Service Layer

**File:** `app/services/skill_service.py`

```python
from app.repositories.skill_repository import skill_repository


class SkillService:

    async def create_skill(self, skill):
        return await skill_repository.create(
            skill.model_dump()
        )

    async def get_all_skills(self):
        return await skill_repository.get_all()

    async def get_skill(self, object_id):
        return await skill_repository.get_by_id(
            object_id
        )

    async def update_skill(
        self,
        object_id,
        skill
    ):
        return await skill_repository.update(
            object_id,
            skill.model_dump()
        )

    async def delete_skill(
        self,
        object_id
    ):
        return await skill_repository.delete(
            object_id
        )


skill_service = SkillService()
```

Notice something?

Currently, the Service Layer only calls the Repository Layer.

That's okay.

Later, we'll add business rules here.

---

# 9. Real Business Logic Example

Suppose SkillHub doesn't allow duplicate skills.

A user already has:

```text
Python
```

User again submits:

```text
Python
```

The Service Layer can check:

```text
Does this skill already exist?

↓

Yes

↓

Raise Exception

↓

Don't insert into MongoDB
```

The API doesn't need to know this rule.

The Repository doesn't need to know this rule.

This is why the Service Layer exists.

---

# 10. Updating the API Layer

Now `app/api/skills.py` becomes much cleaner.

Instead of talking directly to MongoDB:

```python
result = await database.skills.insert_one(...)
```

We call the service:

```python
result = await skill_service.create_skill(skill)
```

Similarly:

```python
skills = await skill_service.get_all_skills()
```

```python
skill = await skill_service.get_skill(object_id)
```

```python
await skill_service.update_skill(object_id, skill)
```

```python
await skill_service.delete_skill(object_id)
```

The API no longer knows **how** the database works.

It only knows **what** it wants.

---

# 11. Complete Request Flow

Let's trace a POST request.

```text
POST /skills

↓

Skills Router

↓

Skill Service

↓

Skill Repository

↓

MongoDB

↓

Insert Result

↓

Skill Service

↓

Skills Router

↓

JSON Response

↓

Client
```

This is exactly how many enterprise applications are structured.

---

# 12. Why Is This Better?

Imagine tomorrow you replace MongoDB with PostgreSQL.

Without Repository Pattern:

You edit database code in:

* skills.py
* users.py
* comments.py
* bookmarks.py

Hundreds of files.

With Repository Pattern:

Only:

```text
SkillRepository

UserRepository

CommentRepository
```

need changes.

The API and Service layers remain unchanged.

---

# 13. Where Will Authentication Go?

Later in the course:

```text
POST /skills

↓

API

↓

Service
   │
   ├── Check Logged-in User
   ├── Check Duplicate Skill
   ├── Add created_by
   ├── Add created_at
   └── Save

↓

Repository

↓

MongoDB
```

Notice how the Service Layer becomes the "brain" of the application.

---

# 14. Current Architecture

```text
                Client
                   │
                   ▼
             FastAPI Router
                   │
                   ▼
            Skill Service
                   │
                   ▼
         Skill Repository
                   │
                   ▼
               MongoDB
```

Every request follows the same path.

---

# 15. Common Mistakes

### ❌ Business logic inside Router

```python
if duplicate_skill:
    ...
```

Move it to the Service Layer.

---

### ❌ Database queries inside Service

```python
database.skills.insert_one(...)
```

Move them to the Repository Layer.

---

### ❌ HTTPException inside Repository

Repositories should not know about HTTP.

Raise or return appropriate results; let higher layers decide how to respond.

---

# 16. Interview Questions

1. What is the Repository Pattern?
2. Why use a Service Layer?
3. What is Separation of Concerns?
4. Why shouldn't API routes directly access the database?
5. If business rules change, which layer should you modify?
6. If the database changes from MongoDB to PostgreSQL, which layer is most affected?

---

# 17. Assignment

Refactor your Skills module:

* Create `SkillRepository`.
* Create `SkillService`.
* Update all CRUD APIs to use the Service Layer.
* Ensure the API still behaves exactly the same after refactoring.

---

# 18. Mini Challenge

Add a business rule:

* Before creating a skill, check if another skill with the same name already exists.
* If it exists, return:

```json
{
  "success": false,
  "message": "Skill already exists"
}
```

**Hint:** Add a new repository method:

```python
async def get_by_name(self, name: str):
```

Then call it from the Service Layer before inserting.

---

# 19. Best Practices

* API Layer → HTTP only.
* Service Layer → Business logic only.
* Repository Layer → Database access only.
* Keep each layer focused on a single responsibility.
* Make business rules reusable by placing them in services.

---

# 20. Summary

Today we transformed our Skills module from a simple CRUD implementation into a **layered architecture** used in many production systems.

We introduced:

* ✅ Repository Pattern
* ✅ Service Layer
* ✅ Separation of Concerns
* ✅ Clean request flow
* ✅ Foundation for authentication and authorization

Our project is now much easier to maintain and extend.

---

# 🚀 Next Lesson

Before adding more collections, we'll improve the Skills API further by implementing features that real-world APIs almost always support:

* Pagination (`page`, `limit`)
* Sorting (ascending/descending)
* Filtering (by category and level)
* Searching (by skill name)
* Query parameters
* Efficient MongoDB queries

By the end of the next lesson, users will be able to perform requests like:

```http
GET /skills?page=2&limit=5&category=Backend&search=python&sort=name
```

This is where our API starts feeling like a real production backend rather than a basic CRUD application.
