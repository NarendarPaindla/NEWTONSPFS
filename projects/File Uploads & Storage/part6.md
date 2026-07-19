# Module: File Uploads & Storage

# Lesson 6 — Production-Ready Local Storage Architecture

In the previous lesson, **all uploaded files** were saved into a single folder:

```text
app/uploads/images/
```

This works for small projects but becomes difficult to manage in real-world applications.

Imagine your SkillHub platform after one year:

* 50,000 Profile Images
* 20,000 Resumes
* 100,000 Course Thumbnails
* 30,000 Certificates
* 10,000 Assignment PDFs

Saving everything in one folder is not scalable.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* Folder organization
* Dynamic storage paths
* Storage Service Layer
* Category-based uploads
* Clean architecture
* Reusable storage functions
* Preparing for AWS S3 migration

---

# Problem with Current Architecture

Current structure:

```text
uploads/
│
├── image1.png
├── image2.jpg
├── resume.pdf
├── assignment.pdf
├── certificate.jpg
├── course.png
├── ....
```

Problems:

❌ Difficult to manage

❌ Difficult to backup

❌ Difficult to delete

❌ Difficult to migrate

---

# Better Architecture

```text
uploads/
│
├── profiles/
│
├── resumes/
│
├── certificates/
│
├── documents/
│
├── course_thumbnails/
│
├── assignments/
│
└── temp/
```

Every file type has its own directory.

---

# Real World Example

LinkedIn

```text
uploads/

profiles/

resumes/

cover_letters/
```

---

Google Classroom

```text
uploads/

assignments/

solutions/

attachments/
```

---

SkillHub

```text
uploads/

profiles/

certificates/

courses/

documents/

assignments/
```

---

# Why Use a Storage Service?

Current code:

```python
destination = UPLOAD_DIR / filename

with destination.open("wb") as buffer:
    shutil.copyfileobj(file.file, buffer)
```

You'll repeat this code in:

* Profile Upload

* Resume Upload

* Course Thumbnail Upload

* Assignment Upload

* Certificate Upload

Instead,

Create one reusable service.

---

# New Project Structure

```text
skillhub-api/

app/

├── api/
│     upload.py
│
├── services/
│     storage_service.py
│
├── uploads/
│
│     profiles/
│
│     resumes/
│
│     certificates/
│
│     documents/
│
│     course_thumbnails/
│
│     assignments/
│
└── main.py
```

---

# Step 1

Create

```text
app/services/storage_service.py
```

---

# Complete File

## app/services/storage_service.py

```python
from pathlib import Path
import shutil
import uuid

from fastapi import UploadFile


class LocalStorageService:

    BASE_DIR = Path("app/uploads")

    @classmethod
    def save_file(
        cls,
        file: UploadFile,
        category: str
    ):
        extension = Path(file.filename).suffix.lower()

        directory = cls.BASE_DIR / category

        directory.mkdir(
            parents=True,
            exist_ok=True
        )

        filename = f"{uuid.uuid4()}{extension}"

        destination = directory / filename

        with destination.open("wb") as buffer:
            shutil.copyfileobj(
                file.file,
                buffer
            )

        return {
            "filename": filename,
            "path": destination
        }
```

---

# Understanding the Service

## Base Directory

```python
BASE_DIR = Path("app/uploads")
```

Everything is stored inside

```text
app/uploads
```

---

## Category

Suppose

```python
category="profiles"
```

Result

```text
app/uploads/profiles
```

---

Suppose

```python
category="documents"
```

Result

```text
app/uploads/documents
```

---

Directory is automatically created.

```python
directory.mkdir(
    parents=True,
    exist_ok=True
)
```

---

UUID filename

```python
filename = f"{uuid.uuid4()}{extension}"
```

---

Save

```python
destination = directory / filename
```

---

# Updated Folder Structure

Initially

```text
uploads/
```

After uploading

Profile

```text
uploads/

profiles/

    123abc.png
```

Resume

```text
uploads/

profiles/

resumes/

    456xyz.pdf
```

Certificate

```text
uploads/

profiles/

resumes/

certificates/

    abc789.jpg
```

Automatically.

---

# Step 2

Update Upload API

Instead of

```python
destination = ...

copyfileobj(...)
```

Simply call

```python
LocalStorageService.save_file()
```

---

# Example

```python
saved = LocalStorageService.save_file(
    file=file,
    category="profiles"
)
```

---

Returned

```python
{
    "filename":"abc123.png",

    "path":Path(...)
}
```

---

Resume Upload

```python
saved = LocalStorageService.save_file(
    file=file,
    category="resumes"
)
```

---

Assignment Upload

```python
saved = LocalStorageService.save_file(
    file=file,
    category="assignments"
)
```

---

Course Thumbnail

```python
saved = LocalStorageService.save_file(
    file=file,
    category="course_thumbnails"
)
```

---

Notice

The upload logic never changes.

Only

```python
category
```

changes.

---

# Storage Flow

```text
Client

    │

    ▼

Upload File

    │

    ▼

Upload API

    │

    ▼

Validation

    │

    ▼

LocalStorageService

    │

    ▼

Determine Category

    │

    ▼

Create Folder

    │

    ▼

Generate UUID

    │

    ▼

Save File

    │

    ▼

Return Path
```

---

# Why This Architecture?

Suppose tomorrow your manager says

> "We are moving from Local Storage to AWS S3."

If saving logic is duplicated in 20 APIs,

you'll modify

20 files.

If saving logic exists only here

```text
storage_service.py
```

You'll modify

**ONE FILE.**

This is called **Separation of Concerns (SoC)**.

---

# Industry Architecture

```text
Controller(API)

        │

        ▼

Validation

        │

        ▼

Service Layer

        │

        ▼

Storage Layer

        │

        ▼

Disk / AWS / Azure / GCP
```

This is the architecture followed in many production systems because it keeps responsibilities isolated and makes storage backends interchangeable.

---

# Best Practices

* ✅ Keep upload validation in the API/service layer.
* ✅ Keep file-saving logic in a dedicated storage service.
* ✅ Organize files by category.
* ✅ Use UUID filenames.
* ✅ Automatically create directories.
* ✅ Design the storage layer so it can be swapped (local today, cloud tomorrow).

---

# Interview Questions

### 1. Why shouldn't all uploaded files be stored in one folder?

It becomes difficult to manage, back up, search, and scale as the number of files grows.

---

### 2. Why create a storage service?

To centralize storage logic, reduce code duplication, and make it easy to change the storage backend.

---

### 3. What is Separation of Concerns (SoC)?

A design principle where different parts of the application handle different responsibilities (e.g., API handles requests, validation checks inputs, storage service saves files).

---

### 4. Why organize uploads by category?

It improves maintainability, organization, and supports different lifecycle policies (e.g., deleting temporary files without affecting profile images).

---

### 5. How does this architecture help when migrating to AWS S3?

Only the storage service needs to change; the API endpoints can continue calling the same service methods.

---

# Module Progress

Completed:

* ✅ UploadFile Fundamentals
* ✅ Single File Upload
* ✅ File Validation
* ✅ Multiple File Upload
* ✅ Production Local Storage Architecture

---

# Next Lesson

We'll implement **Serving Static Files** so uploaded files can be accessed through URLs like:

```text
http://localhost:8000/uploads/profiles/2d4e5f7a.png
```

You'll learn:

* `StaticFiles`
* Mounting static directories
* Public vs. private files
* URL generation for uploaded files
* Security considerations when exposing uploaded content
