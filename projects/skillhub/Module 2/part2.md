# Backend Development with FastAPI + MongoDB

# **Module 2 — Part 2**

# **Production-Ready MongoDB Connection with FastAPI Lifespan**

> **Today's Goal:**
>
> Yesterday, we connected to MongoDB.
>
> Today, we'll do it the way professional FastAPI applications do.

---

# 1. Learning Objectives

After this lesson, you will learn:

* Why startup and shutdown events exist.
* What FastAPI Lifespan is.
* How professional applications manage database connections.
* How to verify MongoDB connectivity using `ping`.
* How to properly close the MongoDB connection.
* Why connection lifecycle management is important.

---

# 2. Concepts Covered

* Application Lifecycle
* Startup
* Shutdown
* Lifespan
* Async Context Manager
* MongoDB Ping
* Connection Pool
* Graceful Shutdown

---

# 3. Why Do We Need Lifespan?

Let's think about a real application.

Suppose your API starts.

```text
python starts

↓

FastAPI starts

↓

MongoDB connection created

↓

API starts serving users
```

Now suppose you stop the application.

What should happen?

```text
Stop Server

↓

Close MongoDB Connection

↓

Release Resources

↓

Exit
```

If we don't close connections properly:

* Memory may remain allocated.
* Network sockets stay open longer than necessary.
* Applications become harder to maintain.

Professional applications always clean up resources.

---

# 4. Old Way vs New Way

### Old Style (Deprecated)

Earlier versions of FastAPI used:

```python
@app.on_event("startup")
```

and

```python
@app.on_event("shutdown")
```

These still exist for backward compatibility, but **FastAPI now recommends the Lifespan API**.

---

### Modern Way

```text
Application Starts

↓

Lifespan Begins

↓

Connect Database

↓

Application Runs

↓

Application Stops

↓

Close Database

↓

Exit
```

This is cleaner and easier to manage.

---

# 5. Current Project Structure

```text
skillhub-api/
│
├── app/
│   ├── api/
│   ├── core/
│   │    config.py
│   ├── db/
│   │    database.py
│   └── main.py
```

We'll update `database.py` and `main.py`.

---

# 6. Understanding the MongoDB Client

Yesterday we wrote:

```python
client = AsyncIOMotorClient(settings.mongodb_url)
```

Many students think:

> "This immediately connects to MongoDB."

Actually, it doesn't.

Motor creates a **client object**.

The connection is established when the first database operation is performed (or when we explicitly test it).

---

# 7. How Can We Test the Connection?

MongoDB provides a lightweight command:

```javascript
ping
```

Just like networking:

```text
Computer

↓

Ping

↓

Server

↓

Server Responds

↓

Connected
```

MongoDB also supports a `ping` command.

---

# 8. Updating `database.py`

**File:** `app/db/database.py`

```python
from motor.motor_asyncio import AsyncIOMotorClient

from app.core.config import settings

client = AsyncIOMotorClient(settings.mongodb_url)

database = client[settings.database_name]


async def connect_to_mongodb():
    await client.admin.command("ping")
    print("✅ Connected to MongoDB")


async def close_mongodb_connection():
    client.close()
    print("❌ MongoDB Connection Closed")
```

---

# 9. Line-by-Line Explanation

## Create Client

```python
client = AsyncIOMotorClient(settings.mongodb_url)
```

Creates the MongoDB client.

---

## Select Database

```python
database = client[settings.database_name]
```

Chooses the database we want to use.

---

## Connect Function

```python
async def connect_to_mongodb():
```

This function is responsible for checking the database connection during application startup.

---

## Ping Command

```python
await client.admin.command("ping")
```

Let's understand this carefully.

`admin`

↓

MongoDB's administrative database.

`command("ping")`

↓

Sends a tiny request to MongoDB asking:

> "Are you alive?"

If MongoDB replies...

Connection is successful.

If not...

An exception is raised.

---

## Close Connection

```python
client.close()
```

This closes all MongoDB connections managed by the client.

---

# 10. Understanding Lifespan

FastAPI provides a **Lifespan Context Manager**.

Think of it like this:

```text
Before yield

↓

Application Starts

↓

Everything Runs

↓

After yield

↓

Application Stops
```

The code **before** `yield` runs during startup.

The code **after** `yield` runs during shutdown.

---

# 11. Update `main.py`

```python
from contextlib import asynccontextmanager

from fastapi import FastAPI

from app.core.config import settings
from app.db.database import (
    connect_to_mongodb,
    close_mongodb_connection,
)


@asynccontextmanager
async def lifespan(app: FastAPI):
    await connect_to_mongodb()

    yield

    await close_mongodb_connection()


app = FastAPI(
    title=settings.project_name,
    version=settings.project_version,
    lifespan=lifespan,
)


@app.get("/")
async def root():
    return {
        "message": "Welcome to SkillHub API"
    }
```

---

# 12. Understanding `@asynccontextmanager`

This decorator tells Python:

> "This function manages resources."

Everything before `yield`

↓

Startup

Everything after `yield`

↓

Shutdown

---

# 13. Visual Flow

```text
Start Application

↓

lifespan()

↓

connect_to_mongodb()

↓

Ping MongoDB

↓

yield

↓

Application Running

↓

Ctrl + C

↓

close_mongodb_connection()

↓

Exit
```

This is exactly what happens internally.

---

# 14. What Will You See?

Run:

```bash
uvicorn app.main:app --reload
```

Console:

```text
✅ Connected to MongoDB

INFO:
Application startup complete.
```

When you stop the server:

```text
❌ MongoDB Connection Closed

INFO:
Application shutdown complete.
```

This confirms that startup and shutdown are working correctly.

---

# 15. Why Is This Better?

Suppose tomorrow we also connect to:

* Redis
* Email Server
* Background Scheduler
* AI Model

With Lifespan:

```text
Startup

↓

Connect MongoDB

↓

Connect Redis

↓

Load AI Model

↓

Start Scheduler

↓

Application Running

↓

Shutdown

↓

Close Everything
```

One place manages all application resources.

---

# 16. What Is a Connection Pool?

When 100 users access SkillHub...

Do we create 100 separate MongoDB connections?

No.

Motor maintains a **connection pool**.

```text
FastAPI

↓

Connection Pool

├── Connection 1
├── Connection 2
├── Connection 3
├── Connection 4

↓

MongoDB
```

Requests reuse connections from the pool, improving performance.

---

# 17. API Testing

Run:

```bash
uvicorn app.main:app --reload
```

Open:

```text
http://127.0.0.1:8000/
```

Expected Response:

```json
{
    "message": "Welcome to SkillHub API"
}
```

The important part today isn't the response—it's the successful startup and shutdown logs in the terminal.

---

# 18. Common Errors

## Error 1

```text
ServerSelectionTimeoutError
```

Reason:

MongoDB is not running.

Fix:

Start the MongoDB service.

---

## Error 2

```text
Connection refused
```

Reason:

Wrong port.

Check:

```env
MONGODB_URL=mongodb://localhost:27017
```

---

## Error 3

```text
Authentication failed
```

Reason:

Incorrect username/password (if your MongoDB instance requires authentication).

---

## Error 4

Nothing prints in the terminal.

Reason:

The `lifespan` function wasn't passed to `FastAPI()`.

Ensure:

```python
app = FastAPI(
    lifespan=lifespan
)
```

---

# 19. Interview Questions

1. What is FastAPI Lifespan?
2. Why are startup and shutdown events important?
3. What does `yield` do inside the lifespan function?
4. Why do we send a MongoDB `ping`?
5. What is a connection pool?
6. Why is `client.close()` important?
7. Does `AsyncIOMotorClient` immediately connect to MongoDB?

---

# 20. Assignment

1. Replace any startup/shutdown event handlers with the Lifespan API.
2. Add `connect_to_mongodb()`.
3. Add `close_mongodb_connection()`.
4. Test that `ping` succeeds.
5. Verify startup and shutdown messages appear in the console.

---

# 21. Mini Challenge

Modify `connect_to_mongodb()` to also print the database name after a successful connection:

```text
✅ Connected to MongoDB
📂 Database: skillhub_db
```

Read the database name from `settings`, not by hardcoding it.

---

# 22. Best Practices

* Use the Lifespan API for resource management in new FastAPI projects.
* Verify external services (like MongoDB) during startup.
* Close resources gracefully during shutdown.
* Keep database connection logic isolated in the `db` package.
* Reuse a single `AsyncIOMotorClient` across the application.

---

# 23. Summary

Today we upgraded our MongoDB integration from a simple client object to a **production-ready connection lifecycle**. We learned how FastAPI's Lifespan API works, why `ping` is useful, how to verify connectivity during startup, and how to close connections gracefully during shutdown.

At this point, our project has:

* ✅ Professional folder structure
* ✅ Environment-based configuration
* ✅ MongoDB connection using Motor
* ✅ Lifespan-based startup and shutdown
* ✅ Connection verification with `ping`

---

# Next Lesson (Very Exciting 🚀)

We'll begin **Module 3 — CRUD APIs** by creating our **first real collection: `skills`**.

We'll learn how to:

* Design the `Skill` document.
* Create **Pydantic v2 request and response schemas**.
* Implement our first **POST `/skills`** API.
* Insert data into MongoDB.
* Return meaningful responses.
* Understand request validation and response models.

From the next lesson onward, we'll stop returning hardcoded data and start building the actual **SkillHub** platform with persistent data.
