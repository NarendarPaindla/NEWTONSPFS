# Module: File Uploads & Cloud Storage

# Lesson 12 — AWS Configuration & Boto3 Setup (Production Ready)

In the previous lesson, we created:

* ✅ AWS Account
* ✅ IAM User
* ✅ Access Key
* ✅ Secret Key
* ✅ Private S3 Bucket

Now it's time to connect our FastAPI application to AWS.

---

# Learning Objectives

By the end of this lesson, you'll learn:

* Install boto3
* Install configuration dependencies
* Create a centralized configuration system
* Store secrets securely using `.env`
* Validate configuration
* Initialize the S3 client
* Test AWS connectivity

---

# Production Architecture

```text
                .env
                  │
                  ▼
        Pydantic Settings
                  │
                  ▼
          Configuration
                  │
                  ▼
             boto3 Client
                  │
                  ▼
            Amazon S3 Bucket
```

Notice:

Every file in the project **never reads environment variables directly**.

Everything goes through one configuration class.

---

# Step 1 — Install Required Packages

```bash
pip install boto3
```

```bash
pip install pydantic-settings
```

If you're using FastAPI with environment variables:

```bash
pip install python-dotenv
```

---

# Updated requirements.txt

```text
fastapi
uvicorn
python-multipart
boto3
pydantic-settings
python-dotenv
```

Install all dependencies:

```bash
pip install -r requirements.txt
```

---

# Updated Project Structure

```text
app/

├── api/
│
├── core/
│     ├── config.py
│     └── __init__.py
│
├── services/
│
├── uploads/
│
├── main.py
│
.env

requirements.txt
```

---

# Step 2 — Create `.env`

Create a file in the project root:

```text
.env
```

Example:

```env
APP_NAME=SkillHub API

DEBUG=True

AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY

AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY

AWS_REGION=ap-south-1

AWS_S3_BUCKET=skillhub-storage

STORAGE_PROVIDER=local
```

### Explanation

```text
APP_NAME
```

Application name.

---

```text
DEBUG
```

Development mode.

---

```text
AWS_ACCESS_KEY_ID
```

IAM Access Key.

---

```text
AWS_SECRET_ACCESS_KEY
```

IAM Secret Key.

---

```text
AWS_REGION
```

Bucket region.

---

```text
AWS_S3_BUCKET
```

Bucket name.

---

```text
STORAGE_PROVIDER
```

Allows switching between:

```text
local
```

or

```text
s3
```

without changing API code.

---

# Never Commit `.env`

Create:

```text
.gitignore
```

```gitignore
.env

__pycache__/

*.pyc

uploads/

venv/
```

---

# Step 3 — Create Configuration Class

Create:

```text
app/core/config.py
```

Complete File

```python
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    APP_NAME: str = "SkillHub API"

    DEBUG: bool = False

    STORAGE_PROVIDER: str = "local"

    AWS_ACCESS_KEY_ID: str

    AWS_SECRET_ACCESS_KEY: str

    AWS_REGION: str

    AWS_S3_BUCKET: str

    model_config = SettingsConfigDict(
        env_file=".env",
        extra="ignore"
    )


settings = Settings()
```

---

# Why Use a Configuration Class?

Instead of:

```python
import os

os.getenv("AWS_ACCESS_KEY_ID")
```

everywhere,

we simply write:

```python
from app.core.config import settings

settings.AWS_REGION
```

Advantages:

* Centralized configuration
* Type validation
* Easier testing
* Cleaner code
* Easier deployment

---

# Step 4 — Test Configuration

Create:

```text
test_config.py
```

```python
from app.core.config import settings

print(settings.APP_NAME)

print(settings.AWS_REGION)

print(settings.AWS_S3_BUCKET)

print(settings.STORAGE_PROVIDER)
```

Run:

```bash
python test_config.py
```

Expected Output

```text
SkillHub API

ap-south-1

skillhub-storage

local
```

---

# Step 5 — Create AWS Service

Create:

```text
app/services/s3_client.py
```

Complete File

```python
import boto3

from app.core.config import settings


s3_client = boto3.client(
    "s3",
    aws_access_key_id=settings.AWS_ACCESS_KEY_ID,
    aws_secret_access_key=settings.AWS_SECRET_ACCESS_KEY,
    region_name=settings.AWS_REGION
)
```

---

# Why Create a Separate S3 Client?

Avoid this:

```python
def upload():
    boto3.client(...)
```

Repeated in every function.

Instead:

```python
from app.services.s3_client import s3_client
```

One client

Shared everywhere

Cleaner architecture.

---

# Step 6 — Test AWS Connection

Create:

```text
test_s3.py
```

```python
from app.services.s3_client import s3_client

from app.core.config import settings


response = s3_client.list_buckets()

print("Connected Successfully")

print()

for bucket in response["Buckets"]:
    print(bucket["Name"])
```

Run:

```bash
python test_s3.py
```

Expected Output

```text
Connected Successfully

skillhub-storage
```

---

# Common Errors

## Error 1

```text
Unable to locate credentials
```

Cause:

Wrong Access Key

Wrong Secret Key

Missing `.env`

---

## Error 2

```text
InvalidAccessKeyId
```

Cause:

Incorrect Access Key.

---

## Error 3

```text
SignatureDoesNotMatch
```

Cause:

Wrong Secret Key.

---

## Error 4

```text
AccessDenied
```

Cause:

IAM User lacks required permissions.

---

## Error 5

```text
NoSuchBucket
```

Cause:

Bucket name is incorrect or the bucket doesn't exist.

---

## Error 6

```text
EndpointConnectionError
```

Cause:

Incorrect AWS region or network connectivity issue.

---

# Configuration Flow

```text
.env

      │

      ▼

Settings()

      │

      ▼

config.py

      │

      ▼

s3_client.py

      │

      ▼

AWS S3
```

---

# Production Best Practices

✅ Use environment variables for secrets.

✅ Never hardcode credentials.

✅ Use a single configuration class.

✅ Reuse a single S3 client.

✅ Keep storage configuration independent from business logic.

---

# Interview Questions

### 1. Why use `BaseSettings` from `pydantic-settings`?

It loads configuration from environment variables while providing validation and type conversion.

---

### 2. Why shouldn't environment variables be accessed throughout the application?

Centralizing configuration improves maintainability, validation, and testing.

---

### 3. Why create a single S3 client?

Creating one shared client avoids duplicate code and improves resource management.

---

### 4. What does `STORAGE_PROVIDER` enable?

It allows switching between storage backends (e.g., local or S3) without changing API endpoints.

---

### 5. Why should `.env` be ignored by Git?

It contains sensitive information such as AWS credentials and should never be committed to source control.

---

# Assignment

1. Install all required packages.
2. Create the `.env` file.
3. Implement `app/core/config.py`.
4. Create `app/services/s3_client.py`.
5. Run `test_config.py`.
6. Run `test_s3.py` and verify your S3 connection.

---

# Git Commit

```bash
git add .
git commit -m "Add centralized AWS configuration and initialize S3 client"
```

---

# Module Progress

Completed:

* ✅ AWS Fundamentals
* ✅ IAM User Setup
* ✅ S3 Bucket Setup
* ✅ Environment Configuration
* ✅ Centralized Settings
* ✅ Shared Boto3 S3 Client

---

# Next Lesson (Lesson 13 — Storage Service Abstraction)

We'll design a production-grade storage architecture using the **Strategy Pattern**:

* Create a `StorageService` interface (abstract base class)
* Implement `LocalStorageService`
* Implement `S3StorageService`
* Build a `StorageFactory`
* Switch between Local Storage and AWS S3 using only `STORAGE_PROVIDER`, with **no changes to API endpoints or business logic**. This is the same architectural pattern commonly used in scalable enterprise applications.
