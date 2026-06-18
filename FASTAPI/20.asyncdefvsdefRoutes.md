# async def vs def Routes in FastAPI

This is one of the most misunderstood topics in FastAPI.

Many students think:

```python
async def
```

means:

```text
Faster
```

and

```python
def
```

means:

```text
Slower
```

❌ Wrong.

The correct answer is:

> Use `async def` only when your code performs asynchronous I/O operations.

Let's understand deeply.

---

# First Look at Both

## Normal Route

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def home():
    return {"message": "Hello"}
```

---

## Async Route

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def home():
    return {"message": "Hello"}
```

Both work.

Both return same response.

So why do we have two options?

---

# Real Life Example

Imagine you're a trainer.

A student asks:

```text
Sir, can you explain Python?
```

You start teaching.

While teaching:

```text
No Waiting
No Pausing
No External Dependency
```

This is similar to:

```python
def
```

---

Now another student asks:

```text
Sir, can you check the library book?
```

You go to library.

While librarian searches:

```text
Waiting...
Waiting...
Waiting...
```

Instead of standing idle, you can teach another student.

This is:

```python
async def
```

---

# What is Synchronous?

```python
def home():
```

Means:

```text
Do work
Wait
Finish
Return
```

One task at a time.

---

Example:

```python
import time

@app.get("/")
def home():

    time.sleep(5)

    return {"message": "Done"}
```

---

Request comes.

Server:

```text
Wait 5 Seconds
Then Respond
```

Worker is blocked.

---

# What is Asynchronous?

```python
async def home():
```

Means:

```text
If waiting occurs,
allow other work to happen.
```

---

Example:

```python
import asyncio

@app.get("/")
async def home():

    await asyncio.sleep(5)

    return {"message": "Done"}
```

---

During:

```python
await asyncio.sleep(5)
```

FastAPI can handle other requests.

This is the magic.

---

# Visual Comparison

## def Route

```text
Request 1
    ↓
Waiting
    ↓
Finish

Request 2
    ↓
Waiting
    ↓
Finish
```

---

## async Route

```text
Request 1
    ↓
Waiting

Request 2
    ↓
Waiting

Request 3
    ↓
Waiting
```

Server efficiently manages them.

---

# When Should You Use def?

Use:

```python
def
```

when your code is:

```text
CPU Work
Calculations
Transformations
Business Logic
```

---

Example

```python
@app.get("/sum")
def calculate():

    total = 0

    for i in range(1000000):
        total += i

    return {"total": total}
```

No waiting.

Pure computation.

Use:

```python
def
```

---

# When Should You Use async def?

Use:

```python
async def
```

when waiting for:

```text
Database
External APIs
Redis
Files
Email Services
Network
```

---

Example

```python
@app.get("/users")
async def users():

    data = await get_users()

    return data
```

---

Because:

```text
Database Response Takes Time
```

---

# Real Industry Example 1

## External API Call

```python
import httpx

@app.get("/weather")
async def weather():

    async with httpx.AsyncClient() as client:

        response = await client.get(
            "https://api.weather.com"
        )

    return response.json()
```

---

Why async?

Because:

```text
Internet Waiting
DNS Waiting
API Waiting
```

---

# Real Industry Example 2

## Database Query

```python
@app.get("/students")
async def students():

    students = await collection.find().to_list()

    return students
```

Database may take:

```text
100ms
500ms
1000ms
```

Async helps.

---

# Real Industry Example 3

## Sending Email

```python
@app.post("/register")
async def register():

    await send_email()

    return {"message":"Registered"}
```

Email service may take time.

Async helps.

---

# Wrong Usage

Many beginners write:

```python
@app.get("/")
async def home():

    total = 0

    for i in range(50000000):
        total += i

    return total
```

---

Question:

Will async make it faster?

Answer:

❌ No

Why?

Because:

```text
No Waiting
Only CPU Work
```

Async gives no benefit.

---

# The await Keyword

Inside:

```python
async def
```

you can use:

```python
await
```

Example:

```python
await database.fetch()
```

---

Meaning:

```text
Pause Here
Do Other Work
Come Back Later
```

---

# Important Rule

This is valid:

```python
async def home():
    await something()
```

---

This is invalid:

```python
def home():
    await something()
```

Error:

```text
SyntaxError
```

Because:

```text
await only works inside async functions
```

---

# FastAPI Internal Behavior

FastAPI handles both.

---

## Normal Function

```python
@app.get("/")
def home():
```

FastAPI executes in a thread pool.

---

## Async Function

```python
@app.get("/")
async def home():
```

FastAPI executes inside event loop.

---

Architecture:

```text
Client
   ↓
Uvicorn
   ↓
Event Loop
   ↓
Async Route
```

---

# Performance Example

Suppose:

100 users call:

```python
await asyncio.sleep(1)
```

---

Async Route

```text
100 Requests
     ↓
~1 Second Total
```

(roughly, simplified)

---

Sync Route

```text
100 Requests
     ↓
Many Workers Needed
```

More resources consumed.

---

# Database Example

## Wrong

```python
@app.get("/students")
def students():

    result = db.find()

    return result
```

If using async database drivers.

---

## Correct

```python
@app.get("/students")
async def students():

    result = await db.find()

    return result
```

---

# MongoDB Example

Using Motor:

```python
@app.get("/students")
async def students():

    data = await collection.find().to_list()

    return data
```

Perfect async usage.

---

# SQLAlchemy Example

Traditional SQLAlchemy:

```python
@app.get("/")
def home():
```

Usually sync.

---

Async SQLAlchemy:

```python
@app.get("/")
async def home():
```

with:

```python
await session.execute(...)
```

---

# Decision Tree

Ask yourself:

### Is there waiting?

```text
Database
API
Redis
Files
Network
```

YES →

```python
async def
```

---

### Only calculations?

```text
Math
Loops
Transformations
```

YES →

```python
def
```

---

# Interview Questions

### What is the difference between `def` and `async def`?

`def` creates a synchronous route.

`async def` creates an asynchronous route.

---

### When should we use `async def`?

When performing I/O-bound operations such as database queries, API calls, file operations, or network communication.

---

### Does `async def` always make code faster?

No.

It mainly improves performance for I/O-bound operations.

---

### Can we use `await` inside `def`?

No.

`await` can only be used inside `async def`.

---

### What happens if there is no `await` inside `async def`?

The route still works, but you gain little or no benefit from making it async.

---

# Real Industry Rule

Most modern FastAPI projects follow:

```python
@app.get("/students")
async def get_students():
```

because they usually interact with:

```text
Databases
Redis
Message Queues
External APIs
Cloud Services
```

which are I/O-bound operations.

---

# Golden Rule

Remember this forever:

```text
CPU Work
    ↓
def

Waiting Work (I/O)
    ↓
async def
```

Examples:

```text
Addition
Loops
Calculations
    ↓
def
```

```text
Database Query
API Call
File Upload
Email Sending
Redis
    ↓
async def
```

This single rule will help you choose the correct route type in almost every FastAPI project.
