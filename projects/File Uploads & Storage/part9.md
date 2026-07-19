# Module: File Uploads & Storage

# Lesson 9 — File Deletion (Production Ready)

In the previous lesson, we learned how to **download files**.

Now we'll learn how to **delete uploaded files** safely.

Deleting a file is more than just calling `os.remove()`.

In a production application, you must answer questions like:

* Does the file exist?
* Is the user allowed to delete it?
* Is the file referenced in the database?
* Should the file be permanently deleted?
* Should it be soft-deleted instead?

---

# Learning Objectives

By the end of this lesson, you'll learn:

* File deletion
* Safe deletion
* Hard Delete
* Soft Delete
* Orphan Files
* Storage cleanup
* Production architecture

---

# Real World Examples

### LinkedIn

Delete Profile Picture

```text
Old Image

↓

Delete Old File

↓

Upload New Image

↓

Save New URL
```

---

### LMS

Delete Assignment

```text
Assignment

↓

Delete Database Record

↓

Delete PDF

↓

Return Success
```

---

### Google Drive

Delete File

↓

Move to

```text
Trash
```

↓

Restore

↓

Permanent Delete

---

# Types of Deletion

## Hard Delete

File is permanently removed.

```text
uploads/

resume.pdf

↓

DELETE

↓

Gone Forever
```

---

## Soft Delete

Database marks

```text
is_deleted=True
```

File still exists.

Later

Cleanup Job

↓

Deletes permanently.

---

# Why Soft Delete?

Suppose user accidentally deletes

```text
Resume.pdf
```

With hard delete

↓

Impossible to recover.

With soft delete

↓

Restore possible.

Most enterprise systems use **soft delete** for user-facing content.

---

# Project Structure

```text
app/

├── api/
│     upload.py
│
├── services/
│     storage_service.py
│
├── uploads/
│
└── main.py
```

---

# Local File Deletion

Python provides

```python
Path.unlink()
```

Example

```python
from pathlib import Path

file = Path("uploads/test.pdf")

file.unlink()
```

File disappears.

---

# Production Storage Service

Update

```text
storage_service.py
```

---

## Complete Updated File

### app/services/storage_service.py

```python
from pathlib import Path
import shutil
import uuid

from fastapi import HTTPException, UploadFile


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

    @classmethod
    def delete_file(
        cls,
        category: str,
        filename: str
    ):
        file_path = cls.BASE_DIR / category / filename

        if not file_path.exists():
            raise HTTPException(
                status_code=404,
                detail="File not found."
            )

        file_path.unlink()

        return {
            "message": "File deleted successfully."
        }
```

---

# Understanding delete_file()

## Step 1

Receive

```text
category

filename
```

Example

```text
profiles

abc123.png
```

---

## Step 2

Construct

```text
app/uploads/profiles/abc123.png
```

---

## Step 3

Check existence

```python
if not file_path.exists():
```

Return

```text
404
```

---

## Step 4

Delete

```python
file_path.unlink()
```

---

# Update Upload API

Add endpoint

```python
@router.delete("/delete/{category}/{filename}")
async def delete_uploaded_file(
    category: str,
    filename: str
):
    return LocalStorageService.delete_file(
        category,
        filename
    )
```

---

# API

```http
DELETE

/upload/delete/profiles/abc123.png
```

---

# Successful Response

```json
{
    "message":"File deleted successfully."
}
```

---

# File Not Found

```json
{
    "detail":"File not found."
}
```

---

# Complete Flow

```text
Client

    │

DELETE Request

    │

    ▼

Authentication

    │

Authorization

    │

Find File

    │

Delete File

    │

Return Success
```

---

# Database + File Synchronization

Suppose

Student Table

```text
id

name

profile_image
```

Contains

```text
uploads/profiles/a.png
```

If you delete

Only

File

↓

Database now points to

```text
Missing File
```

Broken.

---

If you delete

Only

Database

↓

File remains.

Waste of storage.

---

Correct flow

```text
Delete Request

↓

Authentication

↓

Authorization

↓

Delete Database Record

↓

Delete Physical File

↓

Commit Transaction

↓

Return Success
```

If one step fails, handle it carefully so the database and storage stay consistent.

---

# Orphan Files

Example

```text
Database

↓

Deleted

File

↓

Still Exists
```

These unused files are called **orphan files**.

Problems

* Wasted storage
* Higher cloud costs
* Harder backups

Production systems often run scheduled cleanup jobs to find and remove orphaned files.

---

# Safe Deletion

Never trust

```text
filename
```

from user.

Example attack

```text
../../../../windows/system32
```

Always resolve the path and ensure it stays inside the upload directory.

Example

```python
base_dir = cls.BASE_DIR.resolve()
file_path = (base_dir / category / filename).resolve()

if not str(file_path).startswith(str(base_dir)):
    raise HTTPException(
        status_code=400,
        detail="Invalid file path."
    )
```

---

# Soft Delete Architecture

Enterprise applications

Usually

```text
Database

↓

is_deleted=True

↓

Hide From Users

↓

Background Job

↓

Delete Physical File
```

Examples

* Google Drive

* Gmail

* Dropbox

* OneDrive

---

# Hard Delete vs Soft Delete

| Hard Delete               | Soft Delete                |
| ------------------------- | -------------------------- |
| Immediate removal         | Mark as deleted            |
| Cannot restore            | Can restore                |
| Saves storage immediately | Uses storage until cleanup |
| Simpler                   | More flexible              |
| Best for temporary files  | Best for user content      |

---

# Production Best Practices

✅ Authenticate before deletion.

✅ Authorize ownership.

✅ Check file existence.

✅ Prevent path traversal.

✅ Synchronize database and storage.

✅ Use soft delete for user-generated content.

✅ Run cleanup jobs for orphan files.

✅ Log deletion events for auditing.

---

# Interview Questions

### 1. What is the difference between hard delete and soft delete?

* **Hard delete:** Permanently removes the file or record.
* **Soft delete:** Marks it as deleted (e.g., `is_deleted=True`) so it can potentially be restored later.

---

### 2. What are orphan files?

Files that remain in storage but are no longer referenced by any database record.

---

### 3. Why shouldn't you delete only the database record?

Because the physical file remains on disk or cloud storage, wasting space and creating orphan files.

---

### 4. Why is authorization important before deleting a file?

Without authorization, one user could delete another user's files.

---

### 5. Why should you validate the file path before deleting?

To prevent path traversal attacks that attempt to delete files outside the intended upload directory.

---

# Module Progress

Completed:

* ✅ UploadFile Fundamentals
* ✅ Single File Upload
* ✅ File Validation
* ✅ Multiple File Upload
* ✅ Local Storage Architecture
* ✅ Serving Static Files
* ✅ File Downloads
* ✅ File Deletion

---

# Next Lesson

We'll move to **AWS S3 with Boto3**, where you'll learn:

* What Amazon S3 is
* Buckets and Objects
* IAM users and access keys
* Installing and configuring `boto3`
* Uploading files to S3
* Downloading files from S3
* Generating pre-signed URLs
* Replacing local storage with cloud storage using the same storage service architecture
