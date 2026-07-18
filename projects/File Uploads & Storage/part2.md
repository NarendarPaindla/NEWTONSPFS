# Module: File Uploads & Storage

# Lesson 2 — `UploadFile`, `File`, and Your First File Upload API

In this lesson, we'll build our **first file upload endpoint** using FastAPI.

By the end of this lesson, you'll be able to receive uploaded files and inspect their metadata.

---

# Learning Objectives

You'll learn:

* `File`
* `UploadFile`
* `Annotated`
* Receiving uploaded files
* Reading file information
* Testing uploads using Swagger UI
* Testing uploads using Postman

---

# Required Package

FastAPI requires the `python-multipart` package to process `multipart/form-data` requests.

Install it:

```bash
pip install python-multipart
```

Without this package, you'll get an error similar to:

```text
RuntimeError:
Form data requires "python-multipart" to be installed.
```

---

# Project Structure

```text
skillhub-api/
│
├── app/
│   ├── api/
│   │   └── upload.py
│   │
│   └── main.py
│
└── requirements.txt
```

---

# What is `File`?

`File` tells FastAPI:

> "This parameter should come from a multipart file upload."

Example:

```python
file: UploadFile = File(...)
```

Without `File()`, FastAPI treats the parameter like a normal query or body parameter.

---

# What is `Annotated`?

Modern FastAPI recommends using `Annotated`.

Instead of:

```python
file: UploadFile = File(...)
```

Use:

```python
from typing import Annotated

file: Annotated[UploadFile, File()]
```

This separates:

* Type information (`UploadFile`)
* Validation/metadata (`File()`)

It is cleaner and future-proof.

---

# Complete File

## `app/api/upload.py`

```python
from typing import Annotated

from fastapi import APIRouter, File, UploadFile

router = APIRouter(
    prefix="/upload",
    tags=["File Upload"]
)


@router.post("/single")
async def upload_single_file(
    file: Annotated[UploadFile, File()]
):
    return {
        "filename": file.filename,
        "content_type": file.content_type
    }
```

---

# Update `app/main.py`

```python
from fastapi import FastAPI

from app.api.upload import router as upload_router

app = FastAPI(
    title="SkillHub API"
)

app.include_router(upload_router)
```

---

# Run the Application

```bash
uvicorn app.main:app --reload
```

---

# Open Swagger UI

```text
http://127.0.0.1:8000/docs
```

You will see:

```text
POST

/upload/single
```

Unlike a JSON endpoint, Swagger automatically displays a **Choose File** button because the endpoint expects a multipart file.

---

# Test Example

Upload:

```text
profile.png
```

Response:

```json
{
    "filename": "profile.png",
    "content_type": "image/png"
}
```

---

# Understanding `UploadFile`

The object contains useful metadata.

```python
file.filename
```

Returns:

```text
profile.png
```

---

```python
file.content_type
```

Returns:

```text
image/png
```

---

```python
file.file
```

Returns:

```text
SpooledTemporaryFile
```

This is the underlying file object managed by FastAPI.

---

```python
file.headers
```

Returns request headers associated with the uploaded file.

Example:

```text
Content-Disposition:
form-data;
name="file";
filename="profile.png"

Content-Type:
image/png
```

---

# Request Flow

```text
Browser
    │
    ▼
Choose File
    │
    ▼
multipart/form-data
    │
    ▼
FastAPI
    │
    ▼
UploadFile Object
    │
    ▼
Your Endpoint
```

---

# Reading File Contents

To read the uploaded file:

```python
content = await file.read()
```

Example:

```python
from typing import Annotated

from fastapi import APIRouter, File, UploadFile

router = APIRouter(
    prefix="/upload",
    tags=["File Upload"]
)


@router.post("/single")
async def upload_single_file(
    file: Annotated[UploadFile, File()]
):
    content = await file.read()

    return {
        "filename": file.filename,
        "size": len(content)
    }
```

---

# Example Response

```json
{
    "filename": "profile.png",
    "size": 245678
}
```

The size is returned in **bytes**.

---

# Important Note

Calling:

```python
await file.read()
```

reads the entire file.

After that, the file pointer reaches the end of the file.

If you immediately call:

```python
await file.read()
```

again,

you'll get:

```text
b''
```

because there is nothing left to read.

---

# Resetting the Pointer

If you need to read the file again:

```python
await file.seek(0)
```

Example:

```python
content = await file.read()

await file.seek(0)

content_again = await file.read()
```

---

# Closing the File

After processing:

```python
await file.close()
```

FastAPI usually handles cleanup automatically, but explicitly closing files is a good practice when you're finished with them.

---

# Testing with Postman

Method:

```text
POST
```

URL:

```text
http://127.0.0.1:8000/upload/single
```

Body:

```text
form-data
```

Key:

```text
file
```

Type:

```text
File
```

Choose:

```text
profile.png
```

Click **Send**.

---

# Common Mistakes

### ❌ Forgetting `python-multipart`

Result:

```text
RuntimeError

Form data requires
python-multipart
```

---

### ❌ Sending JSON

```json
{
    "file": "profile.png"
}
```

This is **not** a file upload.

---

### ❌ Using the wrong key

Suppose your endpoint expects:

```python
file: UploadFile
```

But Postman sends:

```text
image
```

FastAPI responds with:

```json
{
    "detail": [
        {
            "type": "missing",
            "loc": [
                "body",
                "file"
            ]
        }
    ]
}
```

The form field name must exactly match the parameter name.

---

# Best Practices

* ✅ Use `Annotated`
* ✅ Use `UploadFile`
* ✅ Install `python-multipart`
* ✅ Close files after processing when appropriate
* ✅ Reset the pointer with `seek(0)` if you need to read the file multiple times
* ✅ Validate files before saving them

---

# Interview Questions

### 1. Why is `python-multipart` required?

It enables FastAPI to parse `multipart/form-data` requests containing uploaded files.

---

### 2. What is the difference between `File()` and `UploadFile`?

* `File()` tells FastAPI the value comes from a file upload.
* `UploadFile` represents the uploaded file and provides methods and metadata.

---

### 3. What happens after calling `await file.read()`?

The file pointer moves to the end of the file. Subsequent reads return empty bytes unless you reset the pointer with `await file.seek(0)`.

---

### 4. How do you access the uploaded file's MIME type?

```python
file.content_type
```

---

### 5. How do you get the uploaded file's original name?

```python
file.filename
```

---

# Current Project Status

Completed:

* ✅ File Upload Fundamentals
* ✅ `multipart/form-data`
* ✅ `UploadFile`
* ✅ `File()`
* ✅ `Annotated`
* ✅ Reading uploaded files
* ✅ Swagger testing
* ✅ Postman testing

---

# Next Lesson

We'll build a **production-ready Single File Upload API** that actually saves files to disk. You'll learn:

* `pathlib.Path`
* `uuid` for unique filenames
* Creating upload directories
* Saving uploaded files with `shutil`
* Returning file URLs
* Organizing uploads into folders (images, documents, resumes)
