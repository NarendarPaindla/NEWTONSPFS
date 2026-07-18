# Module: File Uploads & Storage

# Lesson 1 — Understanding File Uploads, `multipart/form-data`, and `UploadFile` Fundamentals

Before writing any code, it's important to understand **how file uploads work** in HTTP and why FastAPI provides the `UploadFile` class.

---

# Learning Objectives

By the end of this lesson, you will understand:

* What is a File Upload?
* How HTTP transfers files
* What is `multipart/form-data`
* Why JSON cannot upload files
* What is `UploadFile`
* `UploadFile` vs `bytes`
* Request lifecycle for file uploads
* Memory management
* Best practices

---

# What is a File Upload?

A file upload is the process of sending one or more files from a client (browser, mobile app, Postman, etc.) to a server.

Examples:

* Uploading a profile picture
* Uploading a resume
* Uploading a PDF assignment
* Uploading a course thumbnail
* Uploading product images
* Uploading videos

---

# Real-World Example

Suppose a student updates their profile.

```text
Student
   │
   ▼
Select Profile Picture
   │
   ▼
Browser
   │
   ▼
HTTP Request
   │
   ▼
FastAPI Server
   │
   ▼
Validate File
   │
   ▼
Save File
   │
   ▼
Database Stores File Path
```

Notice an important detail:

> The **database usually stores the file path or URL**, not the actual file.

Example:

```text
uploads/images/profile_123.jpg
```

or

```text
https://s3.amazonaws.com/skillhub/profile_123.jpg
```

---

# Why Can't We Use JSON?

Many beginners try this:

```json
{
    "name": "Rahul",
    "image": "profile.jpg"
}
```

This only sends the **file name**, not the file contents.

JSON is designed for structured data:

* Strings
* Numbers
* Objects
* Arrays
* Booleans

It is **not designed for transferring binary files** like images, PDFs, videos, or ZIP archives.

---

# What is Binary Data?

Computers store files as binary (bytes).

Example:

```text
Image

↓

1010101001010010100101...
```

The browser must send these bytes to the server.

---

# HTTP Content Types

When a client sends data, it includes a `Content-Type` header.

### JSON Request

```http
Content-Type: application/json
```

Body:

```json
{
    "name": "Rahul"
}
```

---

### File Upload Request

```http
Content-Type: multipart/form-data
```

Body:

```text
Name = Rahul

Photo = profile.png

Resume = resume.pdf
```

Each field is sent as a separate part of the request.

---

# What is `multipart/form-data`?

`multipart/form-data` is a special HTTP format that allows sending:

* Files
* Text fields
* Multiple files
* Mixed content

Example:

```text
POST /upload

Content-Type: multipart/form-data
```

Body:

```text
-------------------------

name = Rahul

-------------------------

email = rahul@gmail.com

-------------------------

photo = profile.png

-------------------------

resume = resume.pdf

-------------------------
```

Unlike JSON, the browser separates each field into its own "part."

---

# Request Flow

```text
Browser
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
Validation
   │
   ▼
Storage
```

---

# What is `UploadFile`?

`UploadFile` is FastAPI's file abstraction for uploaded files.

It gives you access to:

* Original file name
* MIME type
* File contents
* Async read operations
* Efficient streaming

Example attributes:

```text
filename
content_type
file
headers
```

Example:

```text
File Name

↓

profile.png

----------------

Content Type

↓

image/png

----------------

Size

↓

2 MB
```

---

# Why Use `UploadFile` Instead of `bytes`?

FastAPI supports two approaches:

## Option 1 — `bytes`

```python
file: bytes
```

The **entire file is loaded into memory**.

Example:

```text
20 MB Image

↓

20 MB RAM
```

If 100 users upload 20 MB files simultaneously:

```text
20 MB × 100

=

2000 MB RAM
```

This can quickly exhaust server memory.

---

## Option 2 — `UploadFile`

```python
file: UploadFile
```

FastAPI uses a **spooled temporary file**:

* Small files stay in memory.
* Large files are automatically written to a temporary file on disk.

Benefits:

* Lower memory usage
* Better performance
* Suitable for large files
* Production-ready

---

# `bytes` vs `UploadFile`

| Feature                       | bytes | UploadFile                     |
| ----------------------------- | ----- | ------------------------------ |
| Loads entire file into memory | ✅     | ❌ (spools to disk when needed) |
| Suitable for large files      | ❌     | ✅                              |
| Provides filename             | ❌     | ✅                              |
| Provides content type         | ❌     | ✅                              |
| Supports async operations     | ❌     | ✅                              |
| Recommended for production    | ❌     | ✅                              |

For almost all real-world APIs, **`UploadFile` is the preferred choice**.

---

# How `UploadFile` Works Internally

```text
Client
   │
   ▼
multipart/form-data
   │
   ▼
Starlette Parser
   │
   ▼
UploadFile
   │
   ▼
Temporary Storage
   │
   ▼
Your FastAPI Code
```

FastAPI is built on Starlette, which handles parsing multipart requests and creating `UploadFile` instances.

---

# Typical Upload Workflow

```text
Client Uploads File
        │
        ▼
Receive UploadFile
        │
        ▼
Validate Extension
        │
        ▼
Validate MIME Type
        │
        ▼
Validate Size
        │
        ▼
Generate Unique File Name
        │
        ▼
Save File
        │
        ▼
Store Path in Database
        │
        ▼
Return File URL
```

This is the workflow we'll implement throughout the module.

---

# Best Practices

* ✅ Use `UploadFile` for production applications.
* ✅ Validate file type and MIME type.
* ✅ Limit maximum file size.
* ✅ Generate unique filenames (e.g., using UUID).
* ✅ Never trust the original filename.
* ✅ Store files outside your source code directory when possible.
* ✅ Store only the file path or URL in the database.
* ✅ Scan uploads for malware in security-sensitive applications.

---

# Interview Questions

### 1. Why can't JSON upload files?

Because JSON is a text-based format and cannot directly carry binary file data. File uploads require `multipart/form-data`.

---

### 2. What is `multipart/form-data`?

An HTTP request format that allows sending files and regular form fields together in a single request.

---

### 3. Why is `UploadFile` preferred over `bytes`?

`UploadFile` streams file data efficiently, exposes metadata like filename and content type, supports asynchronous operations, and avoids loading large files entirely into memory.

---

### 4. What information does `UploadFile` provide?

* `filename`
* `content_type`
* `file`
* `headers`

---

### 5. What should be stored in the database?

Typically, the **file path or URL**, not the binary file itself.

---

# What We'll Build Next

In the next lesson, we'll write our first upload endpoint and learn how to use:

* `File`
* `UploadFile`
* `Annotated` (recommended modern typing)
* Receiving uploaded files in FastAPI
* Testing uploads with Swagger UI and Postman

We'll build a production-ready **single file upload API** as the foundation for the rest of the module.
