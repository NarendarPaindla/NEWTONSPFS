# Module: File Uploads & Storage

# Lesson 7 — Serving Static Files (Production Ready)

In the previous lesson, we successfully uploaded files and stored them on disk.

However, there is a problem:

The uploaded files exist inside:

```text
app/uploads/profiles/
```

but users **cannot access them through a browser**.

For example:

```
app/uploads/profiles/abc123.png
```

is only a file on the server.

It is **not accessible** via:

```
http://localhost:8000/uploads/profiles/abc123.png
```

To make uploaded files accessible over HTTP, we use **Static File Serving**.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* What are Static Files?
* StaticFiles in FastAPI
* Mounting static directories
* URL Mapping
* Public vs Private Files
* Returning file URLs
* Production Best Practices

---

# What are Static Files?

Static files are files that the server returns **without any processing**.

Examples:

* Images
* PDFs
* CSS
* JavaScript
* Videos
* Audio
* Icons

Example:

```
Client
    │
    ▼
GET /uploads/profile.png
    │
    ▼
FastAPI
    │
    ▼
Return profile.png
```

Unlike API responses, the server does not execute business logic before returning the file.

---

# Before StaticFiles

Suppose we save

```
uploads/profiles/profile.png
```

Trying to open:

```
http://localhost:8000/uploads/profiles/profile.png
```

Result:

```
404 Not Found
```

because FastAPI has no route that serves files from that directory.

---

# How StaticFiles Works

```
Browser

        │

        ▼

GET /uploads/profile.png

        │

        ▼

FastAPI StaticFiles

        │

        ▼

Find File

        │

        ▼

Return File
```

---

# Project Structure

```
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
└── main.py
```

---

# Step 1

Import

```python
from fastapi.staticfiles import StaticFiles
```

---

# Complete Updated File

## `app/main.py`

```python
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles

from app.api.upload import router as upload_router

app = FastAPI(
    title="SkillHub API"
)

app.include_router(upload_router)

app.mount(
    "/uploads",
    StaticFiles(directory="app/uploads"),
    name="uploads"
)
```

---

# Understanding `app.mount()`

```python
app.mount(
    "/uploads",
    StaticFiles(directory="app/uploads"),
    name="uploads"
)
```

### Parameter 1

```python
"/uploads"
```

This is the **URL prefix**.

Browser requests:

```
http://localhost:8000/uploads/...
```

---

### Parameter 2

```python
StaticFiles(directory="app/uploads")
```

This tells FastAPI:

> "Whenever someone requests `/uploads/...`, look inside the `app/uploads` directory."

---

### Parameter 3

```python
name="uploads"
```

A name for this mounted application, useful for URL generation.

---

# URL Mapping

```
Browser URL

/uploads/profiles/photo.png

        │

        ▼

FastAPI

        │

        ▼

app/uploads/profiles/photo.png
```

---

# Example

Saved file:

```
app/uploads/profiles/abc123.png
```

Accessible at:

```
http://localhost:8000/uploads/profiles/abc123.png
```

---

# Returning Public URLs

Instead of returning only the file path:

```json
{
    "path":"app/uploads/profiles/abc123.png"
}
```

Return both the path and the public URL.

Example:

```json
{
    "stored_filename":"abc123.png",
    "path":"app/uploads/profiles/abc123.png",
    "url":"http://localhost:8000/uploads/profiles/abc123.png"
}
```

The frontend should use the **URL**, not the local filesystem path.

---

# Folder Mapping

```
URL

/uploads/profiles/a.png

        │

        ▼

Directory

app/uploads/profiles/a.png
```

```
URL

/uploads/resumes/resume.pdf

        │

        ▼

Directory

app/uploads/resumes/resume.pdf
```

```
URL

/uploads/certificates/certificate.jpg

        │

        ▼

Directory

app/uploads/certificates/certificate.jpg
```

---

# Browser Example

Suppose:

```
uploads/profiles/avatar.png
```

Open

```
http://localhost:8000/uploads/profiles/avatar.png
```

The browser displays the image directly.

If it is a PDF:

```
uploads/documents/python.pdf
```

Open

```
http://localhost:8000/uploads/documents/python.pdf
```

Most browsers will display the PDF inline.

---

# How the Response is Generated

When a browser requests:

```
GET /uploads/profiles/avatar.png
```

FastAPI checks:

```
app/uploads/profiles/avatar.png
```

If found:

```
HTTP 200
```

If not found:

```
HTTP 404
```

No custom API endpoint is required.

---

# Public vs Private Files

Not every uploaded file should be publicly accessible.

### Public Files

Suitable for static serving:

* Profile pictures
* Course thumbnails
* Public certificates
* Company logos

```
Browser

↓

Direct URL

↓

Image
```

---

### Private Files

Should **not** be exposed through `StaticFiles`:

* Aadhaar cards
* Passports
* Salary slips
* Medical reports
* Internal company documents

These should be served through authenticated API endpoints that check permissions before returning the file.

---

# Security Considerations

* ✅ Only expose directories intended to be public.
* ✅ Keep sensitive uploads outside the mounted directory or protect them with authenticated endpoints.
* ✅ Continue validating uploaded files before saving them.
* ✅ Do not expose server directory structures in API responses if unnecessary.

---

# Best Practices

* Use `StaticFiles` only for publicly accessible content.
* Store private documents separately.
* Return URLs instead of filesystem paths to frontend clients.
* Organize uploads by category.
* Use UUID filenames to avoid collisions.
* Consider a CDN (e.g., CloudFront) for serving static files in production.

---

# Interview Questions

### 1. What is `StaticFiles` in FastAPI?

`StaticFiles` is an ASGI application that serves static assets such as images, PDFs, CSS, JavaScript, and other files directly from a directory.

---

### 2. What does `app.mount()` do?

It mounts another ASGI application (such as `StaticFiles`) at a specified URL path.

---

### 3. Why shouldn't private documents be served through `StaticFiles`?

Because anyone who knows the URL can access them. Sensitive files should be protected by authentication and authorization.

---

### 4. Why should APIs return file URLs instead of local file paths?

URLs can be consumed by frontend applications, while filesystem paths are meaningful only to the server.

---

### 5. How does FastAPI find a static file?

It maps the requested URL path to the configured directory and returns the matching file if it exists.

---

# Module Progress

Completed:

* ✅ UploadFile Fundamentals
* ✅ Single File Upload
* ✅ File Validation
* ✅ Multiple File Upload
* ✅ Local Storage Architecture
* ✅ Serving Static Files

---

# Next Lesson

We'll implement **File Downloads** using `FileResponse` and `StreamingResponse`. You'll learn:

* `FileResponse`
* `StreamingResponse`
* Download vs inline display
* Setting `Content-Disposition`
* Secure download endpoints
* Downloading private files with authentication and authorization
