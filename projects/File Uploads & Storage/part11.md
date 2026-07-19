# Module: File Uploads & Storage

# Lesson 11 — AWS S3 Upload with Boto3 (Production Implementation)

In the previous lesson, we learned the theory behind Amazon S3 and Boto3.

In this lesson, we'll **integrate AWS S3 into our FastAPI project** using a production-style architecture.

Our goal is to replace local storage with cloud storage **without changing our API endpoints**.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* Configure Boto3
* Create an S3 client
* Upload files to S3
* Organize objects by category
* Return S3 URLs
* Handle upload errors
* Integrate S3 into the Storage Service layer

---

# Architecture

```text
                Client
                   │
                   ▼
          Upload API Endpoint
                   │
                   ▼
            File Validation
                   │
                   ▼
          Storage Service Layer
          ┌────────────────────┐
          │                    │
          ▼                    ▼
    Local Storage         AWS S3 (Boto3)
          │                    │
          └────────────┬───────┘
                       ▼
                 Return File URL
```

Notice that **the upload API doesn't know whether the file is stored locally or in S3**.

---

# Step 1 — Install Dependencies

```bash
pip install boto3
```

If you're using environment variables:

```bash
pip install python-dotenv
```

---

# Step 2 — Environment Variables

Create a `.env` file:

```text
AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY
AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY
AWS_REGION=ap-south-1
AWS_S3_BUCKET=skillhub-storage
```

> Never commit your `.env` file to Git. Add it to `.gitignore`.

---

# Step 3 — Project Structure

```text
skillhub-api/
│
├── app/
│   ├── api/
│   │     upload.py
│   │
│   ├── core/
│   │     config.py
│   │
│   ├── services/
│   │     storage_service.py
│   │
│   └── main.py
│
├── .env
└── requirements.txt
```

---

# Step 4 — Configuration

## `app/core/config.py`

```python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    aws_access_key_id: str
    aws_secret_access_key: str
    aws_region: str
    aws_s3_bucket: str

    class Config:
        env_file = ".env"


settings = Settings()
```

---

# Step 5 — Create S3 Client

Inside `storage_service.py`

```python
import boto3

from app.core.config import settings

s3_client = boto3.client(
    "s3",
    aws_access_key_id=settings.aws_access_key_id,
    aws_secret_access_key=settings.aws_secret_access_key,
    region_name=settings.aws_region,
)
```

This client will be reused for every upload.

---

# Step 6 — Updated Storage Service

## `app/services/storage_service.py`

```python
from pathlib import Path
import uuid

import boto3
from fastapi import HTTPException, UploadFile
from botocore.exceptions import ClientError

from app.core.config import settings


class S3StorageService:

    s3_client = boto3.client(
        "s3",
        aws_access_key_id=settings.aws_access_key_id,
        aws_secret_access_key=settings.aws_secret_access_key,
        region_name=settings.aws_region,
    )

    @classmethod
    def save_file(
        cls,
        file: UploadFile,
        category: str,
    ):
        extension = Path(file.filename).suffix.lower()

        filename = f"{uuid.uuid4()}{extension}"

        object_key = f"{category}/{filename}"

        try:
            cls.s3_client.upload_fileobj(
                Fileobj=file.file,
                Bucket=settings.aws_s3_bucket,
                Key=object_key,
                ExtraArgs={
                    "ContentType": file.content_type,
                },
            )

        except ClientError as exc:
            raise HTTPException(
                status_code=500,
                detail="Failed to upload file to S3."
            ) from exc

        file_url = (
            f"https://{settings.aws_s3_bucket}.s3."
            f"{settings.aws_region}.amazonaws.com/{object_key}"
        )

        return {
            "filename": filename,
            "object_key": object_key,
            "url": file_url,
        }
```

---

# Understanding the Code

## Generate UUID

```python
filename = f"{uuid.uuid4()}{extension}"
```

Example:

```text
3df7d7d0-2e52-4b35-b75e-8cb7fd6d4c2b.png
```

---

## Object Key

```python
object_key = f"{category}/{filename}"
```

Result:

```text
profiles/3df7d7d0-2e52-4b35-b75e-8cb7fd6d4c2b.png
```

Inside S3:

```text
skillhub-storage

│

├── profiles/

│      avatar.png

│

├── resumes/

│      resume.pdf

│

├── certificates/

│      cert.pdf
```

---

## Upload File

```python
upload_fileobj(...)
```

Parameters:

| Parameter   | Purpose                       |
| ----------- | ----------------------------- |
| `Fileobj`   | Uploaded file stream          |
| `Bucket`    | Target S3 bucket              |
| `Key`       | Object key inside bucket      |
| `ExtraArgs` | Metadata such as content type |

---

## Content Type

```python
ExtraArgs={
    "ContentType": file.content_type
}
```

If omitted:

S3 stores the file as:

```text
application/octet-stream
```

With `ContentType`, browsers can correctly render:

* Images
* PDFs
* Videos

---

# Upload Flow

```text
User Uploads Image

        │

        ▼

FastAPI Validation

        │

        ▼

Storage Service

        │

        ▼

Generate UUID

        │

        ▼

Create Object Key

        │

        ▼

Boto3 upload_fileobj()

        │

        ▼

Amazon S3 Bucket

        │

        ▼

Return Object URL
```

---

# Example Response

```json
{
    "filename": "3df7d7d0.png",
    "object_key": "profiles/3df7d7d0.png",
    "url": "https://skillhub-storage.s3.ap-south-1.amazonaws.com/profiles/3df7d7d0.png"
}
```

---

# Error Handling

Possible upload failures include:

* Invalid AWS credentials
* Bucket not found
* Permission denied
* Network issues
* Region mismatch

Always catch SDK exceptions and return meaningful API errors instead of exposing internal details.

---

# Production Enhancements

In production, you can improve the service by:

* Adding retries for transient failures.
* Logging upload events.
* Encrypting objects (SSE-S3 or SSE-KMS).
* Adding object tags and metadata.
* Using lifecycle policies for archival and cleanup.
* Uploading directly from the client with pre-signed URLs (covered next).

---

# Local Storage vs S3

| Local Storage       | Amazon S3                       |
| ------------------- | ------------------------------- |
| Server disk         | Cloud object storage            |
| Single server       | Shared across servers           |
| Manual backup       | Built-in durability             |
| Limited scalability | Virtually unlimited scalability |
| Local file path     | Object key + URL                |

---

# Best Practices

* ✅ Store only the object key or URL in the database.
* ✅ Generate UUID filenames.
* ✅ Organize objects by category.
* ✅ Set the correct `ContentType`.
* ✅ Catch AWS SDK exceptions.
* ✅ Keep S3 logic inside the storage service.

---

# Interview Questions

### 1. What does `upload_fileobj()` do?

It uploads a file-like object directly to an S3 bucket without requiring a temporary file on disk.

---

### 2. Why use UUIDs for S3 object names?

To prevent collisions and avoid exposing user-supplied filenames.

---

### 3. What is an object key?

The unique path-like identifier of an object inside an S3 bucket, such as `profiles/avatar.png`.

---

### 4. Why set the `ContentType` when uploading?

It allows browsers and clients to correctly interpret and display the uploaded file.

---

### 5. Why keep S3 logic inside a storage service?

It separates storage concerns from API logic, making the application easier to maintain and allowing storage backends to be swapped with minimal changes.

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
* ✅ AWS S3 Fundamentals
* ✅ AWS S3 Upload with Boto3

---

# Next Lesson

We'll implement **Pre-Signed URLs** for Amazon S3. This is the approach used by many production applications because it allows clients to upload and download files directly from S3 without routing large file transfers through the FastAPI server, improving scalability and reducing server load.
