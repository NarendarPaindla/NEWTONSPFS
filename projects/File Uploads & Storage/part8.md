# Module: File Uploads & Storage

# Lesson 8 — File Downloads (`FileResponse` & `StreamingResponse`)

In the previous lesson, we learned how to **serve public files** using `StaticFiles`.

Example:

```text
http://localhost:8000/uploads/profiles/profile.png
```

This works well for **public** files.

However, many real-world applications require **controlled downloads**.

Examples:

* Download Resume
* Download Assignment
* Download Invoice
* Download Certificate
* Download Report
* Download Medical Record

For these scenarios, we use **FileResponse** or **StreamingResponse** instead of exposing files through `StaticFiles`.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* What is FileResponse?
* What is StreamingResponse?
* Download vs View in Browser
* Content-Disposition Header
* Download Endpoint
* Secure File Downloads
* Production Download Architecture

---

# Why Not Use StaticFiles for Everything?

Suppose a student uploads:

```text
resume.pdf
```

If it's inside:

```text
/uploads/resumes/
```

Anyone with the URL could download it.

Example:

```text
http://localhost:8000/uploads/resumes/abc123.pdf
```

This is **not secure**.

Instead:

```text
Client

↓

GET /download/resume/abc123.pdf

↓

Authentication

↓

Authorization

↓

Return File
```

This allows you to check:

* Is the user logged in?
* Does the file exist?
* Does the user own the file?
* Does the user have permission?

---

# StaticFiles vs FileResponse

| StaticFiles         | FileResponse            |
| ------------------- | ----------------------- |
| Public files        | Secure files            |
| No authentication   | Authentication possible |
| URL-based access    | API-based access        |
| Automatic serving   | Full control            |
| Good for images/CSS | Good for documents      |

---

# What is FileResponse?

`FileResponse` is a FastAPI response class that sends a file from disk to the client.

Example:

```python
from fastapi.responses import FileResponse
```

Instead of returning JSON:

```json
{
    "message":"Success"
}
```

the endpoint returns:

```text
resume.pdf
```

---

# Project Structure

```text
app/

├── api/
│      upload.py
│
├── uploads/
│
│      profiles/
│
│      resumes/
│
│      certificates/
│
└── main.py
```

---

# Complete Updated File

## app/api/upload.py

Add the following imports:

```python
from fastapi.responses import FileResponse
```

```python
from pathlib import Path
```

---

## Add Download Endpoint

```python
@router.get("/download/{category}/{filename}")
async def download_file(
    category: str,
    filename: str
):
    file_path = Path("app/uploads") / category / filename

    if not file_path.exists():
        raise HTTPException(
            status_code=404,
            detail="File not found."
        )

    return FileResponse(
        path=file_path,
        filename=filename,
        media_type="application/octet-stream"
    )
```

---

# Understanding the Code

## Step 1

Receive URL

```text
/download/profiles/abc123.png
```

Parameters

```python
category="profiles"

filename="abc123.png"
```

---

## Step 2

Build Path

```python
file_path = Path("app/uploads") / category / filename
```

Result

```text
app/uploads/profiles/abc123.png
```

---

## Step 3

Check File Exists

```python
if not file_path.exists():
```

Return

```text
404 Not Found
```

if missing.

---

## Step 4

Return File

```python
return FileResponse(
    path=file_path,
    filename=filename
)
```

FastAPI automatically:

* Reads file
* Sets headers
* Sends file
* Closes file

---

# Test

Request

```http
GET

/download/profiles/abc123.png
```

Browser

↓

Download starts.

---

# What is Content-Disposition?

When downloading files,

HTTP adds

```http
Content-Disposition
```

Example

```http
Content-Disposition:

attachment;

filename="resume.pdf"
```

This tells the browser

> Download the file.

---

# Inline Display

Suppose

```python
FileResponse(
    path=file_path,
    media_type="image/png"
)
```

Browser displays image.

---

Suppose

```python
FileResponse(
    path=file_path,
    filename="resume.pdf"
)
```

Browser downloads.

---

# Download Flow

```text
Browser

      │

      ▼

GET /download/file

      │

      ▼

Authentication

      │

      ▼

Authorization

      │

      ▼

Locate File

      │

      ▼

FileResponse

      │

      ▼

Download
```

---

# What is StreamingResponse?

Suppose

Video

```text
4 GB
```

Reading entire video into memory

↓

Very expensive.

Instead

Use

```python
StreamingResponse
```

It sends data

```text
Chunk 1

↓

Chunk 2

↓

Chunk 3

↓

Chunk 4
```

instead of loading the entire file into RAM.

---

# Example

```python
from fastapi.responses import StreamingResponse

def iter_file(path):
    with open(path, "rb") as file:
        while chunk := file.read(1024 * 1024):
            yield chunk

@router.get("/stream/{category}/{filename}")
async def stream_file(category: str, filename: str):
    file_path = Path("app/uploads") / category / filename

    if not file_path.exists():
        raise HTTPException(status_code=404, detail="File not found.")

    return StreamingResponse(
        iter_file(file_path),
        media_type="application/octet-stream"
    )
```

This reads the file **1 MB at a time**, making it suitable for very large files.

---

# FileResponse vs StreamingResponse

| FileResponse      | StreamingResponse |
| ----------------- | ----------------- |
| Simple files      | Very large files  |
| Automatic headers | Manual control    |
| Easy to use       | More flexible     |
| Good for PDFs     | Good for videos   |
| Good for images   | Good for backups  |

---

# Production Security

Never expose

```text
/download/{filename}
```

without authentication.

Instead

```text
User Login

↓

JWT Verify

↓

Database Check

↓

Owner?

↓

Download
```

Example

Student A

cannot download

Student B's

resume.

---

# Path Traversal Attack

Suppose user requests

```text
../../../../etc/passwd
```

Never directly trust

```python
filename
```

Instead

* Validate filename
* Restrict category
* Resolve paths safely
* Ensure the resolved path stays inside the upload directory

For example:

```python
base_dir = Path("app/uploads").resolve()
file_path = (base_dir / category / filename).resolve()

if not str(file_path).startswith(str(base_dir)):
    raise HTTPException(status_code=400, detail="Invalid file path.")
```

This helps prevent directory traversal attacks.

---

# Best Practices

* ✅ Use `FileResponse` for normal downloads.
* ✅ Use `StreamingResponse` for very large files.
* ✅ Check file existence before returning it.
* ✅ Authenticate and authorize access to private files.
* ✅ Protect against path traversal attacks.
* ✅ Prefer UUID filenames over user-supplied names.

---

# Interview Questions

### 1. What is `FileResponse`?

A FastAPI response class that sends a file from disk directly to the client.

---

### 2. When should you use `StreamingResponse`?

When serving very large files or generated content that should be sent incrementally without loading everything into memory.

---

### 3. What does the `Content-Disposition` header do?

It tells the browser whether the file should be displayed inline or downloaded as an attachment.

---

### 4. Why shouldn't sensitive documents be served with `StaticFiles`?

Because anyone with the URL can access them. Sensitive files should be protected with authentication and authorization.

---

### 5. What is a path traversal attack?

An attack where a user manipulates the requested path (e.g., using `../`) to access files outside the intended directory. Proper path validation and resolution are required to prevent it.

---

# Module Progress

Completed:

* ✅ UploadFile Fundamentals
* ✅ Single File Upload
* ✅ File Validation
* ✅ Multiple File Upload
* ✅ Local Storage Architecture
* ✅ Serving Static Files
* ✅ File Downloads (`FileResponse` & `StreamingResponse`)

---

# Next Lesson

We'll implement **File Deletion** with a production-ready approach. You'll learn:

* Safe file deletion
* Deleting files from local storage
* Synchronizing database records and storage
* Soft delete vs. hard delete
* Cleanup strategies for orphaned files
* Preparing the deletion logic for cloud storage backends like AWS S3
