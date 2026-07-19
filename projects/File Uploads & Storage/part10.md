# Module: File Uploads & Storage

# Lesson 10 — AWS S3 Fundamentals & Boto3 (Production Ready)

Up to now, we've stored files on the **local server**.

That works well for development and small deployments.

However, in production, storing files on the application server has several limitations:

* If the server crashes, uploaded files may be lost.
* Scaling to multiple servers becomes difficult because each server has its own local filesystem.
* Backups and disaster recovery are harder.
* Storage capacity is limited by the server's disk.

This is why most production applications use **object storage**, and one of the most popular options is **Amazon S3**.

---

# Learning Objectives

By the end of this lesson, you'll understand:

* What is AWS?
* What is Amazon S3?
* What is Boto3?
* Buckets
* Objects
* Regions
* IAM Users
* Access Keys
* Storage Classes
* Why cloud storage is preferred over local storage

---

# What is AWS?

**Amazon Web Services (AWS)** is a cloud computing platform that provides services such as:

* Compute (EC2)
* Databases (RDS)
* Object Storage (S3)
* Networking (VPC)
* Monitoring (CloudWatch)
* Serverless Computing (Lambda)

For file storage, we'll use **Amazon S3**.

---

# What is Amazon S3?

**Amazon Simple Storage Service (S3)** is an object storage service designed to store and retrieve files from anywhere over the internet.

Examples of files stored in S3:

* Images
* Videos
* PDFs
* ZIP files
* Audio
* Backups
* Machine Learning datasets

---

# Real-World Companies Using Object Storage

Many modern platforms rely on cloud object storage (often S3 or S3-compatible services) for user-uploaded content.

Examples include:

* Social media platforms for profile pictures and media
* Learning management systems for assignments and certificates
* E-commerce platforms for product images
* Video platforms for thumbnails and media assets

---

# Local Storage vs Amazon S3

| Local Storage                     | Amazon S3                        |
| --------------------------------- | -------------------------------- |
| Stored on application server      | Stored in AWS cloud              |
| Limited disk space                | Highly scalable                  |
| Difficult to share across servers | Accessible from multiple servers |
| Server failure can affect files   | High durability and redundancy   |
| Manual backups                    | Built-in durability features     |

---

# S3 Terminology

## Bucket

A **bucket** is a container for objects.

Example:

```text
Bucket

skillhub-storage
```

Think of it like a top-level folder.

---

## Object

Every uploaded file is an **object**.

Example:

```text
Bucket

skillhub-storage

│

├── profiles/avatar.png

├── resumes/resume.pdf

├── certificates/python.pdf
```

Unlike local storage, S3 doesn't have real folders. The `/` characters are part of the object's **key**.

---

# Object Key

Example:

```text
profiles/avatar.png
```

The key uniquely identifies an object inside a bucket.

---

# Bucket URL

Example structure:

```text
https://skillhub-storage.s3.amazonaws.com/profiles/avatar.png
```

Your application stores this URL or the object key in the database.

---

# Regions

AWS resources exist in specific geographic regions.

Examples:

* us-east-1
* eu-west-1
* ap-south-1 (Mumbai)
* ap-southeast-1 (Singapore)

Choosing a region close to your users reduces latency.

---

# IAM (Identity and Access Management)

Your application should **not** use your AWS root account.

Instead:

1. Create an IAM User.
2. Grant only the required S3 permissions.
3. Generate an Access Key and Secret Access Key.

Example permissions:

* `s3:PutObject`
* `s3:GetObject`
* `s3:DeleteObject`

This follows the **Principle of Least Privilege**.

---

# Access Keys

Your FastAPI application authenticates to AWS using:

```text
AWS_ACCESS_KEY_ID
```

and

```text
AWS_SECRET_ACCESS_KEY
```

These credentials should **never** be hardcoded into your source code.

Store them in environment variables or a secure secret manager.

---

# Storage Classes

Amazon S3 offers different storage classes based on access frequency and cost.

Examples:

| Storage Class              | Use Case                              |
| -------------------------- | ------------------------------------- |
| Standard                   | Frequently accessed files             |
| Intelligent-Tiering        | Unknown access patterns               |
| Standard-IA                | Infrequently accessed files           |
| Glacier Instant Retrieval  | Archived files with occasional access |
| Glacier Flexible Retrieval | Long-term archival                    |
| Glacier Deep Archive       | Rarely accessed archival data         |

For profile pictures, resumes, and certificates, **S3 Standard** is typically appropriate.

---

# Typical Upload Flow

```text
Client

    │

Upload File

    │

FastAPI

    │

Validation

    │

Storage Service

    │

Boto3

    │

Amazon S3 Bucket

    │

Return Object URL
```

---

# What is Boto3?

**Boto3** is the official AWS SDK for Python.

It allows Python applications to interact with AWS services such as:

* S3
* DynamoDB
* SNS
* SQS
* EC2

We'll use it to:

* Upload files
* Download files
* Delete files
* Generate pre-signed URLs

---

# Install Boto3

```bash
pip install boto3
```

---

# Environment Variables

A typical `.env` file might contain:

```text
AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY
AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY
AWS_REGION=ap-south-1
AWS_S3_BUCKET=skillhub-storage
```

Your application should load these values from configuration rather than hardcoding them.

---

# Storage Service Architecture

One of the advantages of the architecture we've built is that only the storage layer changes.

```text
Controller

        │

Validation

        │

Storage Service

      ┌───────────────┐
      │               │
      ▼               ▼

Local Storage     Amazon S3

(Path)            (Boto3)
```

The upload API can continue calling:

```python
storage_service.save_file(...)
```

Whether the implementation saves locally or uploads to S3 is hidden behind the storage service.

---

# Why This Design Is Important

Suppose your application grows from:

* 100 users

to

* 1,000,000 users

You may decide to move from local storage to S3.

If every API endpoint contains storage logic, you'll need to update many files.

If all storage logic is centralized in one service, you mainly modify that service while keeping the API endpoints unchanged.

---

# Best Practices

* ✅ Use IAM users instead of the AWS root account.
* ✅ Grant only the minimum required S3 permissions.
* ✅ Store AWS credentials in environment variables or a secrets manager.
* ✅ Organize S3 object keys by category (e.g., `profiles/`, `resumes/`).
* ✅ Keep storage operations inside a dedicated service layer.
* ✅ Avoid exposing AWS credentials in logs or source code.

---

# Interview Questions

### 1. What is Amazon S3?

Amazon S3 is a scalable object storage service used to store and retrieve files over the internet.

---

### 2. What is a bucket?

A bucket is a top-level container that stores objects in Amazon S3.

---

### 3. What is an object key?

An object key is the unique identifier of a file within an S3 bucket, such as `profiles/avatar.png`.

---

### 4. What is Boto3?

Boto3 is the official AWS SDK for Python that enables applications to interact with AWS services.

---

### 5. Why shouldn't AWS credentials be hardcoded?

Hardcoding credentials is a security risk. Credentials should be stored securely in environment variables or secret management systems.

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

---

# Next Lesson

We'll implement **AWS S3 Uploads using Boto3** in our FastAPI project. You'll learn how to:

* Configure a Boto3 S3 client
* Connect FastAPI to your S3 bucket
* Upload files directly to S3
* Return the uploaded object's key and URL
* Integrate S3 into the existing `StorageService` architecture without changing the API endpoints
