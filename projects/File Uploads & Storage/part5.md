# Module: File Uploads & Storage

# Lesson 5 — Multiple File Uploads (Production Ready)

In the previous lesson, our API could upload **one file**.

Most real-world applications need to upload **multiple files** in a single request.

Examples:

* Gmail → Multiple attachments
* Google Drive → Upload multiple documents
* LinkedIn → Multiple certificates
* LMS → Assignment with multiple supporting files
* E-Commerce → Multiple product images
* Job Portal → Resume + Cover Letter + Certificates

In this lesson, we'll build a production-ready API that accepts multiple files, validates each one, and stores them safely.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* `list[UploadFile]`
* Uploading multiple files
* Looping through uploaded files
* Validating each file
* Saving multiple files
* Returning upload results
* Fail-fast vs. Partial Success strategies

---

# Upload Flow

```text
          Client
             │
             ▼
     Select Multiple Files
             │
             ▼
    multipart/form-data
             │
             ▼
   FastAPI receives List[UploadFile]
             │
             ▼
   Validate Each File
             │
             ▼
     Generate UUID
             │
             ▼
      Save Each File
             │
             ▼
 Return Uploaded File Details
```

---

# API Design

We'll create a new endpoint:

```http
POST /upload/multiple
```

The request will contain:

```text
file1 = profile.png
file2 = resume.pdf
file3 = certificate.jpg
```

FastAPI automatically converts them into:

```python
list[UploadFile]
```

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

Only **`app/api/upload.py`** changes.

---

# Complete Updated File

## `app/api/upload.py`

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


def validate_file(file: UploadFile, size: int):
    extension = Path(file.filename).suffix.lower()

    if extension not in ALLOWED_EXTENSIONS:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=f"{file.filename}: Unsupported file extension."
        )

    if file.content_type not in ALLOWED_CONTENT_TYPES:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=f"{file.filename}: Invalid content type."
        )

    if size == 0:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=f"{file.filename}: Empty files are not allowed."
        )

    if size > MAX_FILE_SIZE:
        raise HTTPException(
            status_code=status.HTTP_413_REQUEST_ENTITY_TOO_LARGE,
            detail=f"{file.filename}: Maximum file size is 5 MB."
        )


@router.post("/single")
async def upload_single_file(
    file: Annotated[UploadFile, File()]
):
    content = await file.read()

    validate_file(file, len(content))

    await file.seek(0)

    extension = Path(file.filename).suffix.lower()

    filename = f"{uuid.uuid4()}{extension}"

    destination = UPLOAD_DIR / filename

    with destination.open("wb") as buffer:
        shutil.copyfileobj(file.file, buffer)

    await file.close()

    return {
        "message": "File uploaded successfully",
        "original_filename": file.filename,
        "stored_filename": filename,
        "content_type": file.content_type,
        "size": len(content),
        "path": str(destination)
    }


@router.post("/multiple")
async def upload_multiple_files(
    files: Annotated[list[UploadFile], File()]
):
    uploaded_files = []

    for file in files:

        content = await file.read()

        validate_file(file, len(content))

        await file.seek(0)

        extension = Path(file.filename).suffix.lower()

        filename = f"{uuid.uuid4()}{extension}"

        destination = UPLOAD_DIR / filename

        with destination.open("wb") as buffer:
            shutil.copyfileobj(file.file, buffer)

        await file.close()

        uploaded_files.append(
            {
                "original_filename": file.filename,
                "stored_filename": filename,
                "content_type": file.content_type,
                "size": len(content),
                "path": str(destination)
            }
        )

    return {
        "message": "All files uploaded successfully",
        "total_files": len(uploaded_files),
        "files": uploaded_files
    }
```

---

# Understanding the Changes

## Receiving Multiple Files

```python
files: Annotated[list[UploadFile], File()]
```

Instead of receiving one `UploadFile`, FastAPI provides a list.

Example:

```text
[
    profile.png,
    resume.pdf,
    certificate.jpg
]
```

---

## Processing Each File

```python
for file in files:
```

The loop processes one uploaded file at a time.

---

## Validation

Each file is validated independently:

```python
validate_file(file, len(content))
```

This avoids duplicating validation logic.

---

## Saving

Each file gets its own UUID filename:

```python
e2d34a8b.png
b61d9c3e.pdf
c8f4d7a1.jpg
```

No filename collisions occur.

---

# Swagger UI

Open:

```text
http://127.0.0.1:8000/docs
```

Endpoint:

```text
POST /upload/multiple
```

Swagger will display a file selector that allows choosing **multiple files**.

---

# Example Request

Upload:

```text
profile.png

resume.pdf

certificate.jpg
```

---

# Example Response

```json
{
  "message": "All files uploaded successfully",
  "total_files": 3,
  "files": [
    {
      "original_filename": "profile.png",
      "stored_filename": "2d4e5f7a.png",
      "content_type": "image/png",
      "size": 245678,
      "path": "app/uploads/images/2d4e5f7a.png"
    },
    {
      "original_filename": "resume.pdf",
      "stored_filename": "5a8c3d1e.pdf",
      "content_type": "application/pdf",
      "size": 152344,
      "path": "app/uploads/images/5a8c3d1e.pdf"
    },
    {
      "original_filename": "certificate.jpg",
      "stored_filename": "8f1b7d2c.jpg",
      "content_type": "image/jpeg",
      "size": 452987,
      "path": "app/uploads/images/8f1b7d2c.jpg"
    }
  ]
}
```

---

# Fail-Fast Strategy

The current implementation uses a **fail-fast** approach.

Example:

```text
File 1 → Valid ✅

File 2 → Valid ✅

File 3 → Invalid ❌

Request Stops
```

The API returns an error immediately, and the remaining files are not processed.

**Pros:**

* Simple implementation
* Easy to maintain
* Suitable when all uploaded files are required

**Cons:**

* Files uploaded before the failure remain saved unless you implement cleanup (transaction-like behavior).

---

# Partial Success Strategy

Some applications prefer to process every file and report individual results.

Example response:

```json
{
  "uploaded": [
    "profile.png",
    "resume.pdf"
  ],
  "failed": [
    {
      "file": "virus.exe",
      "reason": "Unsupported file extension"
    }
  ]
}
```

This is useful for cloud storage systems, galleries, and document management platforms.

---

# Best Practices

* ✅ Reuse validation logic through helper functions.
* ✅ Generate unique filenames for every file.
* ✅ Validate every uploaded file independently.
* ✅ Close each uploaded file after processing.
* ✅ Decide early whether your API should use a fail-fast or partial-success strategy.
* ✅ Consider rolling back previously saved files if one upload fails and atomic behavior is required.

---

# Interview Questions

### 1. How do you receive multiple uploaded files in FastAPI?

```python
files: Annotated[list[UploadFile], File()]
```

---

### 2. Why create a separate validation function?

To avoid repeating the same validation logic in multiple endpoints, making the code easier to maintain and test.

---

### 3. What is the difference between fail-fast and partial success?

* **Fail-fast:** Stop processing on the first error.
* **Partial success:** Continue processing and report successes and failures separately.

---

### 4. Why use UUIDs for every uploaded file?

To prevent filename collisions and avoid overwriting existing files.

---

### 5. What additional consideration is needed with fail-fast uploads?

If one file fails after others have already been saved, you may need a cleanup mechanism to delete the previously saved files if you want all-or-nothing behavior.

---

# Module Progress

Completed:

* ✅ UploadFile Fundamentals
* ✅ Single File Upload
* ✅ Local File Storage
* ✅ File Validation
* ✅ Multiple File Upload

---

# Next Lesson

We'll organize uploads into a **production-ready storage structure** by category. You'll learn:

* Separate folders for images, resumes, certificates, and documents
* Dynamic folder selection
* `pathlib.Path` directory management
* Service-based storage architecture
* Preparing the project for future cloud storage (AWS S3, Azure Blob, Google Cloud Storage)
