# Module: File Uploads & Storage

# Lesson 3 — Production-Ready Single File Upload (Local Storage)

In the previous lesson, we received an uploaded file but did **not** save it anywhere.

In this lesson, we'll build a **production-ready file upload API** that:

* Accepts an uploaded file
* Creates the upload directory automatically
* Generates a unique filename
* Saves the file to disk
* Returns the uploaded file's information

This is the foundation for profile pictures, resumes, course thumbnails, and document uploads.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* `pathlib.Path`
* `uuid`
* `shutil.copyfileobj()`
* Creating upload directories
* Saving files safely
* Generating unique filenames
* Returning file metadata

---

# Why Not Save the Original Filename?

Suppose two users upload:

```text
profile.png
```

If you save the original filename:

```text
uploads/images/profile.png
```

The second upload overwrites the first one.

---

# Better Approach

Generate a unique filename.

Example:

```text
Original

profile.png

↓

Stored

5d98d0d4-1a2b-4b4e-a6f4-6d5d6e5a3d2f.png
```

Every uploaded file has a unique name.

---

# Project Structure

```text
skillhub-api/
│
├── app/
│   ├── api/
│   │   └── upload.py
│   │
│   ├── uploads/
│   │   └── images/
│   │
│   └── main.py
│
└── requirements.txt
```

---

# Why `pathlib`?

Instead of:

```python
import os

os.path.join("uploads", "images")
```

Use:

```python
from pathlib import Path

UPLOAD_DIR = Path("app/uploads/images")
```

Advantages:

* Cleaner syntax
* Cross-platform
* Object-oriented
* Recommended in modern Python

---

# Complete File

## `app/api/upload.py`

```python
from pathlib import Path
from typing import Annotated
import shutil
import uuid

from fastapi import APIRouter, File, UploadFile

router = APIRouter(
    prefix="/upload",
    tags=["File Upload"]
)

UPLOAD_DIR = Path("app/uploads/images")

# Create the directory if it doesn't exist
UPLOAD_DIR.mkdir(parents=True, exist_ok=True)


@router.post("/single")
async def upload_single_file(
    file: Annotated[UploadFile, File()]
):
    # Get original extension
    extension = Path(file.filename).suffix

    # Generate unique filename
    filename = f"{uuid.uuid4()}{extension}"

    # Full destination path
    destination = UPLOAD_DIR / filename

    # Save file
    with destination.open("wb") as buffer:
        shutil.copyfileobj(file.file, buffer)

    await file.close()

    return {
        "message": "File uploaded successfully",
        "original_filename": file.filename,
        "stored_filename": filename,
        "content_type": file.content_type,
        "path": str(destination)
    }
```

---

# Understanding Each Step

## Step 1

```python
UPLOAD_DIR = Path("app/uploads/images")
```

Creates a path object.

Result:

```text
app/uploads/images
```

---

## Step 2

```python
UPLOAD_DIR.mkdir(
    parents=True,
    exist_ok=True
)
```

Automatically creates:

```text
app/

uploads/

images/
```

Even if the folders don't already exist.

---

## Step 3

```python
extension = Path(file.filename).suffix
```

Examples:

```text
profile.png

↓

.png
```

```text
resume.pdf

↓

.pdf
```

---

## Step 4

```python
uuid.uuid4()
```

Example:

```text
8a56bc76-9a54-46aa-9fd6-53e20bcb6b97
```

---

## Step 5

```python
filename = f"{uuid.uuid4()}{extension}"
```

Result:

```text
8a56bc76-9a54-46aa-9fd6-53e20bcb6b97.png
```

---

## Step 6

```python
destination = UPLOAD_DIR / filename
```

Result:

```text
app/uploads/images/

↓

8a56bc76-9a54-46aa-9fd6-53e20bcb6b97.png
```

---

## Step 7

```python
with destination.open("wb") as buffer:
```

Opens the destination file in **binary write mode**.

---

## Step 8

```python
shutil.copyfileobj(
    file.file,
    buffer
)
```

Copies the uploaded file stream to the destination file.

Flow:

```text
UploadFile

↓

Temporary File

↓

Destination File

↓

Disk
```

This is memory-efficient because it copies the stream instead of reading the entire file into memory.

---

# Run the Application

```bash
uvicorn app.main:app --reload
```

---

# Test in Swagger

Open:

```text
http://127.0.0.1:8000/docs
```

Endpoint:

```text
POST

/upload/single
```

Upload:

```text
profile.png
```

---

# Response

```json
{
    "message": "File uploaded successfully",
    "original_filename": "profile.png",
    "stored_filename": "8a56bc76-9a54-46aa-9fd6-53e20bcb6b97.png",
    "content_type": "image/png",
    "path": "app/uploads/images/8a56bc76-9a54-46aa-9fd6-53e20bcb6b97.png"
}
```

---

# Folder After Upload

```text
app/
│
├── uploads/
│   └── images/
│       ├── 7e2f8b0a.png
│       ├── 13bc55d1.png
│       ├── 52de6f11.jpg
│       └── 9c62aa80.pdf
```

---

# Current Flow

```text
Client
   │
   ▼
Upload File
   │
   ▼
FastAPI
   │
   ▼
Generate UUID
   │
   ▼
Create Folder
   │
   ▼
Save File
   │
   ▼
Return Metadata
```

---

# Common Mistakes

## ❌ Saving the original filename

```python
destination = UPLOAD_DIR / file.filename
```

This risks overwriting existing files.

---

## ❌ Not closing the uploaded file

```python
await file.close()
```

Closing the file releases resources promptly.

---

## ❌ Using `file.read()` for very large files

```python
content = await file.read()
```

This loads the entire file into memory.

For saving uploads, prefer:

```python
shutil.copyfileobj(file.file, buffer)
```

which streams the file efficiently.

---

# Best Practices

* ✅ Generate unique filenames with UUID.
* ✅ Preserve the original extension when appropriate.
* ✅ Use `pathlib.Path` instead of `os.path`.
* ✅ Create directories automatically with `mkdir()`.
* ✅ Stream files to disk using `shutil.copyfileobj()`.
* ✅ Close uploaded files after processing.
* ❌ Never trust the original filename for storage.

---

# Interview Questions

### 1. Why use UUID for uploaded files?

To avoid filename collisions and accidental overwriting.

---

### 2. Why use `pathlib.Path` instead of `os.path`?

It provides a cleaner, object-oriented, and cross-platform API for filesystem paths.

---

### 3. Why is `shutil.copyfileobj()` preferred for saving uploads?

It efficiently copies file streams without loading the entire file into memory.

---

### 4. Why should you preserve the file extension?

The extension helps identify the file type and is often useful for serving or processing the file later.

---

### 5. Why shouldn't you store files using the original filename?

Different users may upload files with the same name, leading to collisions and overwritten files.

---

# Current Project Status

Completed:

* ✅ File Upload Fundamentals
* ✅ `multipart/form-data`
* ✅ `UploadFile`
* ✅ Reading uploaded files
* ✅ Production-ready single file upload
* ✅ Local file storage
* ✅ UUID-based filenames

---

# Next Lesson

We'll make this upload endpoint **secure** by adding comprehensive file validation. You'll learn how to validate:

* Allowed file extensions
* MIME types
* Maximum file size
* Empty files
* Invalid uploads

This will transform the upload endpoint into a production-ready API that rejects unsafe or invalid files before saving them.
