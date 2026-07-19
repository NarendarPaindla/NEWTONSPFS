# Module: File Uploads & Storage

# Lesson 4 — File Validation (Production Ready)

In the previous lesson, we successfully uploaded files.

However, our API has a major security issue:

❌ It accepts **any file**.

That means users could upload:

* `.exe` files
* `.bat` files
* Malware
* Huge files (10 GB)
* Empty files
* Fake image files

A production application should **never** trust uploaded files without validation.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* File Extension Validation
* MIME Type Validation
* File Size Validation
* Empty File Validation
* Unique Filename Generation
* Returning Proper HTTP Errors
* Production Upload Security

---

# Upload Flow

```text
                Client
                   │
                   ▼
           Upload File
                   │
                   ▼
        Check File Extension
                   │
             Valid / Invalid
                   │
                   ▼
         Check MIME Type
                   │
             Valid / Invalid
                   │
                   ▼
         Check File Size
                   │
             Valid / Invalid
                   │
                   ▼
         Generate UUID
                   │
                   ▼
            Save File
                   │
                   ▼
           Return Response
```

---

# Validation 1 — Allowed Extensions

Suppose our API only accepts:

```text
jpg
jpeg
png
pdf
```

If someone uploads

```text
virus.exe
```

Immediately reject it.

---

## Allowed Extensions

```python
ALLOWED_EXTENSIONS = {
    ".jpg",
    ".jpeg",
    ".png",
    ".pdf"
}
```

---

# Validation 2 — MIME Type

Never trust only the filename.

Suppose someone renames

```text
virus.exe
```

to

```text
virus.png
```

The extension becomes

```text
.png
```

But the MIME Type may still be

```text
application/x-msdownload
```

UploadFile provides

```python
file.content_type
```

Example

```text
image/png
image/jpeg
application/pdf
```

---

## Allowed MIME Types

```python
ALLOWED_CONTENT_TYPES = {
    "image/png",
    "image/jpeg",
    "application/pdf"
}
```

---

# Validation 3 — Maximum File Size

Suppose users upload

```text
10 GB Video
```

Your server memory and disk can quickly fill up.

Let's allow only

```text
5 MB
```

---

## Maximum Size

```python
MAX_FILE_SIZE = 5 * 1024 * 1024
```

Which equals

```text
5,242,880 bytes
```

---

# Validation 4 — Empty Files

Someone uploads

```text
empty.pdf
```

Size

```text
0 bytes
```

Reject it.

---

# Updated Project Structure

```text
app/
│
├── api/
│   └── upload.py
│
├── uploads/
│   └── images/
│
└── main.py
```

---

# Complete File

## app/api/upload.py

```python
from pathlib import Path
from typing import Annotated
import shutil
import uuid

from fastapi import (
    APIRouter,
    File,
    HTTPException,
    UploadFile,
    status,
)

router = APIRouter(
    prefix="/upload",
    tags=["File Upload"]
)

UPLOAD_DIR = Path("app/uploads/images")
UPLOAD_DIR.mkdir(parents=True, exist_ok=True)

ALLOWED_EXTENSIONS = {
    ".jpg",
    ".jpeg",
    ".png",
    ".pdf",
}

ALLOWED_CONTENT_TYPES = {
    "image/jpeg",
    "image/png",
    "application/pdf",
}

MAX_FILE_SIZE = 5 * 1024 * 1024


@router.post("/single")
async def upload_single_file(
    file: Annotated[UploadFile, File()]
):
    # -----------------------------
    # Validate Extension
    # -----------------------------
    extension = Path(file.filename).suffix.lower()

    if extension not in ALLOWED_EXTENSIONS:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Unsupported file extension."
        )

    # -----------------------------
    # Validate MIME Type
    # -----------------------------
    if file.content_type not in ALLOWED_CONTENT_TYPES:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Invalid content type."
        )

    # -----------------------------
    # Validate File Size
    # -----------------------------
    content = await file.read()

    file_size = len(content)

    if file_size == 0:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Empty files are not allowed."
        )

    if file_size > MAX_FILE_SIZE:
        raise HTTPException(
            status_code=status.HTTP_413_REQUEST_ENTITY_TOO_LARGE,
            detail="Maximum allowed file size is 5 MB."
        )

    # Reset pointer
    await file.seek(0)

    # -----------------------------
    # Generate UUID Filename
    # -----------------------------
    filename = f"{uuid.uuid4()}{extension}"

    destination = UPLOAD_DIR / filename

    # -----------------------------
    # Save File
    # -----------------------------
    with destination.open("wb") as buffer:
        shutil.copyfileobj(file.file, buffer)

    await file.close()

    return {
        "message": "File uploaded successfully",
        "original_filename": file.filename,
        "stored_filename": filename,
        "content_type": file.content_type,
        "size": file_size,
        "path": str(destination),
    }
```

---

# Understanding Each Validation

## Extension Validation

```python
extension = Path(file.filename).suffix.lower()
```

Example

```text
Resume.PDF

↓

.pdf
```

---

```python
if extension not in ALLOWED_EXTENSIONS:
```

Rejects

```text
.exe

.bat

.cmd

.sh
```

---

# MIME Validation

```python
file.content_type
```

Example

```text
image/png
```

Rejects

```text
application/x-msdownload
```

---

# Size Validation

```python
content = await file.read()
```

Reads the uploaded file.

---

```python
len(content)
```

Returns

```text
245678 bytes
```

---

# Reset Pointer

Since we already read the file,

```python
await file.seek(0)
```

moves the pointer back to the beginning.

Otherwise,

```python
shutil.copyfileobj()
```

would save an empty file.

---

# Successful Upload

Response

```json
{
    "message": "File uploaded successfully",
    "original_filename": "profile.png",
    "stored_filename": "ae91d23f-6d2c-4f43-82f5-1b6e3c6b0c52.png",
    "content_type": "image/png",
    "size": 245678,
    "path": "app/uploads/images/ae91d23f-6d2c-4f43-82f5-1b6e3c6b0c52.png"
}
```

---

# Invalid Extension

Upload

```text
virus.exe
```

Response

```json
{
    "detail": "Unsupported file extension."
}
```

---

# Invalid MIME Type

Response

```json
{
    "detail": "Invalid content type."
}
```

---

# Large File

Upload

```text
20 MB Video
```

Response

```json
{
    "detail": "Maximum allowed file size is 5 MB."
}
```

HTTP Status

```text
413 Payload Too Large
```

---

# Empty File

Upload

```text
empty.pdf
```

Response

```json
{
    "detail": "Empty files are not allowed."
}
```

---

# Security Notes

⚠️ **Extension validation alone is not enough.**

An attacker can rename:

```text
virus.exe
```

to

```text
virus.png
```

⚠️ **MIME type validation alone is also not enough.**

A malicious client can forge the `Content-Type` header.

For high-security applications, inspect the **actual file signature (magic bytes)** using libraries such as `python-magic` or validate image files with libraries like `Pillow`. We'll cover stronger validation in advanced lessons.

---

# Best Practices

* ✅ Validate extension
* ✅ Validate MIME type
* ✅ Validate file size
* ✅ Reject empty files
* ✅ Generate UUID filenames
* ✅ Return proper HTTP status codes
* ✅ Reset the file pointer after reading
* ✅ Close uploaded files

---

# Interview Questions

### 1. Why shouldn't you trust the file extension?

Because users can rename malicious files to have safe-looking extensions.

---

### 2. Why isn't MIME type validation sufficient by itself?

Because the client supplies the `Content-Type` header, and it can be spoofed.

---

### 3. Why do we call `await file.seek(0)`?

After reading the file for validation, the file pointer is at the end. Resetting it allows the file to be saved correctly.

---

### 4. Which HTTP status code is appropriate for an oversized upload?

**413 Payload Too Large**.

---

### 5. Why use UUID filenames?

To prevent filename collisions and avoid exposing user-supplied filenames on disk.

---

# Module Progress

Completed:

* ✅ UploadFile Fundamentals
* ✅ Single File Upload
* ✅ Local File Storage
* ✅ File Validation (Extension, MIME Type, Size, Empty File)

---

# Next Lesson

We'll implement **Multiple File Uploads**, where users can upload several files in a single request. You'll learn:

* `list[UploadFile]`
* Handling multiple files
* Validating each file individually
* Saving multiple files efficiently
* Returning upload results for each file
* Partial success vs. fail-fast upload strategies
