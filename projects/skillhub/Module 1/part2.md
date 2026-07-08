# Module 1 — Part 2

# Environment Setup & Creating Our First FastAPI Application

> **Goal:** By the end of this lesson, you will have the **SkillHub API** running locally with automatic Swagger documentation.

---

# 1. Learning Objectives

After this lesson, you will be able to:

* Create a backend project from scratch.
* Understand why virtual environments are important.
* Install FastAPI and required packages.
* Create a clean project structure.
* Create your first FastAPI application.
* Run the application using Uvicorn.
* Open Swagger UI.
* Understand what happens when the server starts.

---

# 2. Concepts Covered

* Project Setup
* Virtual Environment
* pip
* requirements.txt
* Uvicorn
* FastAPI Application
* HTTP Server
* ASGI
* Hot Reload
* Swagger UI
* OpenAPI

---

# 3. Why Do We Need These Concepts?

Imagine you are working in a company.

Company A's project:

* Python 3.11
* FastAPI 0.116
* Motor 3.7

Company B's project:

* Python 3.13
* FastAPI 0.118
* Motor 4.x

If you install everything globally, package versions will conflict.

**Solution:** A **Virtual Environment**.

Each project gets its own isolated Python environment.

```text
Global Python
      │
      ├───────────────┐
      │               │
      ▼               ▼
SkillHub API      Another Project
venv              venv
FastAPI           Django
Motor             Flask
```

This isolation prevents dependency conflicts.

---

# 4. Project Changes

Today we'll create the project skeleton.

```text
skillhub-api/
│
├── app/
│   └── main.py
│
├── .env
├── requirements.txt
├── .gitignore
└── README.md
```

This is enough to get started.

---

# 5. Step 1 – Create Project Folder

Open Terminal.

```bash
mkdir skillhub-api
```

Move inside it.

```bash
cd skillhub-api
```

---

# 6. Step 2 – Open VS Code

```bash
code .
```

Now VS Code opens the project.

---

# 7. Step 3 – Create Virtual Environment

Windows

```bash
python -m venv venv
```

Linux / Mac

```bash
python3 -m venv venv
```

Project becomes

```text
skillhub-api/
│
├── venv/
```

---

# 8. What is Inside `venv`?

```text
venv/
│
├── Include/
├── Lib/
├── Scripts/
├── pyvenv.cfg
```

Don't edit these files manually.

Python creates them automatically.

---

# 9. Step 4 – Activate Virtual Environment

### Windows

```bash
venv\Scripts\activate
```

Terminal becomes

```text
(venv)
C:\skillhub-api>
```

### Linux / macOS

```bash
source venv/bin/activate
```

The `(venv)` prefix means your virtual environment is active.

---

# 10. How Does Activation Work?

Without activation:

```text
python
│
▼
Global Python
```

With activation:

```text
python
│
▼
Project Python
│
▼
venv
```

All installed packages now stay inside this project.

---

# 11. Step 5 – Install Required Packages

We'll start with the minimum required packages.

```bash
pip install fastapi uvicorn
```

Then install MongoDB-related packages.

```bash
pip install motor pymongo
```

Install validation library.

```bash
pip install pydantic pydantic-settings
```

Install environment variable support.

```bash
pip install python-dotenv
```

---

# 12. Verify Installed Packages

Run:

```bash
pip list
```

Example output:

```text
fastapi
motor
pymongo
uvicorn
pydantic
python-dotenv
```

---

# 13. Step 6 – Freeze Dependencies

Create `requirements.txt`.

```bash
pip freeze > requirements.txt
```

Example:

```text
fastapi==0.xx.x
motor==3.x.x
pymongo==4.x.x
uvicorn==0.xx.x
pydantic==2.x.x
python-dotenv==1.x.x
```

> We use `pip freeze` so anyone else can install the exact same versions.

---

# 14. Step 7 – Create Folder Structure

```text
skillhub-api/
│
├── app/
│
├── requirements.txt
├── .env
├── README.md
└── .gitignore
```

Inside `app`, create:

```text
app/
└── main.py
```

---

# 15. Step 8 – Write Our First FastAPI Application

**File:** `app/main.py`

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {
        "message": "Welcome to SkillHub API 🚀"
    }
```

---

# 16. Line-by-Line Explanation

### Line 1

```python
from fastapi import FastAPI
```

Imports the `FastAPI` class.

---

### Line 3

```python
app = FastAPI()
```

Creates the FastAPI application object.

Think of it as the main entry point of your backend.

---

### Line 6

```python
@app.get("/")
```

Creates an API endpoint.

`GET /`

means

```
http://localhost:8000/
```

---

### Line 7

```python
async def root():
```

Defines an asynchronous function.

We'll learn **why `async` matters** in later modules.

---

### Line 8

```python
return {
    "message": "Welcome to SkillHub API 🚀"
}
```

Returns JSON.

FastAPI automatically converts the Python dictionary into a JSON response.

---

# 17. Why Do We Use `async` From Day One?

FastAPI is designed for asynchronous programming.

Even though this endpoint is simple, using `async` now means we won't need to rewrite it later when we connect to MongoDB with Motor.

---

# 18. Step 9 – Run the Server

Run:

```bash
uvicorn app.main:app --reload
```

Let's understand this command:

* `uvicorn` → ASGI server.
* `app.main` → Python module (`app/main.py`).
* `:app` → FastAPI instance (`app = FastAPI()`).
* `--reload` → Automatically restarts the server when files change.

---

# 19. Expected Output

```text
INFO:     Uvicorn running on http://127.0.0.1:8000
INFO:     Started reloader process
INFO:     Started server process
INFO:     Application startup complete.
```

If you see this, your server is running successfully.

---

# 20. Open the Browser

Visit:

```text
http://127.0.0.1:8000/
```

Response:

```json
{
    "message": "Welcome to SkillHub API 🚀"
}
```

---

# 21. Explore Swagger UI

Now open:

```text
http://127.0.0.1:8000/docs
```

You'll see FastAPI's interactive documentation.

Why is this amazing?

* Lists all APIs automatically.
* Lets you test APIs without Postman.
* Shows request and response formats.
* Updates automatically when your code changes.

This is powered by the **OpenAPI** specification.

---

# 22. What is OpenAPI?

OpenAPI is a standard for describing REST APIs.

FastAPI generates this documentation automatically from your code.

Benefits:

* No manual documentation.
* Easier frontend integration.
* Better API testing.
* Cleaner developer experience.

---

# 23. API Testing

Test the root endpoint in Swagger UI:

* Click **GET /**.
* Click **Try it out**.
* Click **Execute**.

Expected response:

```json
{
    "message": "Welcome to SkillHub API 🚀"
}
```

Status Code:

```text
200 OK
```

---

# 24. Common Errors

### Error 1

```text
'uvicorn' is not recognized
```

**Cause:** Uvicorn is not installed or the virtual environment is not activated.

**Fix:**

```bash
pip install uvicorn
```

Activate the virtual environment and try again.

---

### Error 2

```text
ModuleNotFoundError: No module named 'fastapi'
```

**Cause:** FastAPI is not installed in the active virtual environment.

**Fix:**

```bash
pip install fastapi
```

---

### Error 3

```text
Address already in use
```

**Cause:** Port `8000` is already occupied.

**Fix:**

```bash
uvicorn app.main:app --reload --port 8001
```

---

# 25. Interview Questions

1. What is a virtual environment?
2. Why shouldn't packages be installed globally?
3. What is Uvicorn?
4. What does `--reload` do?
5. Why does FastAPI use `async` functions?
6. What is Swagger UI?
7. What is OpenAPI?

---

# 26. Assignment

1. Create the `skillhub-api` project.
2. Create and activate a virtual environment.
3. Install all required packages.
4. Generate `requirements.txt`.
5. Run the FastAPI application.
6. Open `/docs` and test the root endpoint.

---

# 27. Mini Challenge

Enhance the application by adding two more endpoints:

* `GET /health` → Returns the API health status.
* `GET /about` → Returns information about the SkillHub project (e.g., project name, version, and description).

Try implementing these on your own before looking up examples.

---

# 28. Best Practices

* Always work inside a virtual environment.
* Commit `requirements.txt` to version control.
* Use `--reload` only during development.
* Keep the project structure organized from the beginning.
* Verify APIs with Swagger after every change.

---

# 29. Summary

In this lesson, we:

* Created the `skillhub-api` project.
* Set up a Python virtual environment.
* Installed FastAPI and supporting packages.
* Created the first FastAPI application.
* Ran the server with Uvicorn.
* Accessed the root endpoint.
* Explored Swagger UI and understood how FastAPI generates API documentation automatically.

---

## Next Lesson

We'll continue with **Module 1 – Part 3**, where we'll:

* Organize the project into professional folders.
* Create a reusable configuration system.
* Add a `.env` file.
* Read environment variables using **Pydantic v2 Settings**.
* Introduce the first production-ready application configuration before connecting to MongoDB.
