# Module: Security & Middleware

# Lesson 7 — Common API Vulnerabilities (OWASP API Security)

In this lesson, you'll learn the **most common security vulnerabilities** found in APIs and how to prevent them in FastAPI.

These concepts are based on the **OWASP API Security Top 10**, which is widely used in industry to identify and mitigate API security risks.

---

# Learning Objectives

By the end of this lesson, you will understand:

* Broken Authentication
* Broken Authorization
* NoSQL Injection
* Sensitive Data Exposure
* Mass Assignment
* Security Misconfiguration
* Brute Force Attacks
* File Upload Vulnerabilities
* Input Validation
* Best practices to secure APIs

---

# 1. Broken Authentication

## What is it?

Broken Authentication occurs when attackers can impersonate legitimate users because authentication is implemented incorrectly.

Examples:

* Weak passwords
* Tokens without expiration
* Predictable JWT secrets
* Tokens stored insecurely
* Missing multi-factor authentication (for sensitive applications)

### Example (Insecure)

```python
SECRET_KEY = "123456"
```

This is easy to guess.

### Secure

```python
SECRET_KEY = os.getenv("SECRET_KEY")
```

Generate a strong random secret (at least 32 bytes) and store it in environment variables, not in source code.

---

## Another Example

Bad JWT

```text
Never Expires
```

If stolen, it can be used indefinitely.

Better

```text
Expires in 15 minutes
```

Use refresh tokens if longer sessions are needed.

---

# FastAPI Best Practices

* Hash passwords with bcrypt/Argon2
* Use HTTPS
* Short-lived JWT access tokens
* Strong secret keys
* Store secrets in environment variables
* Rotate keys when appropriate

---

# 2. Broken Authorization

Authentication answers:

> Who are you?

Authorization answers:

> What are you allowed to do?

---

## Example

User A

```text
GET /users/10
```

User B

```text
GET /users/10
```

If User B receives User A's data, you have a broken authorization vulnerability.

---

## Incorrect Code

```python
@app.get("/users/{user_id}")
async def get_user(user_id: int):
    return db.get(user_id)
```

Anyone who knows an ID can access another user's information.

---

## Secure Approach

```python
if current_user.id != user_id:
    raise HTTPException(
        status_code=403,
        detail="Access denied"
    )
```

Always verify that the authenticated user is authorized to access the requested resource.

---

# 3. NoSQL Injection

Since SkillHub uses MongoDB, NoSQL Injection is especially relevant.

### Dangerous Input

```json
{
    "email": {
        "$ne": ""
    },
    "password": {
        "$ne": ""
    }
}
```

If your code blindly passes user input to a MongoDB query, an attacker might bypass expected query logic.

---

## Unsafe Example

```python
collection.find_one(request.json())
```

The entire client payload becomes the database query.

---

## Safe Approach

Use Pydantic models and explicitly construct queries:

```python
collection.find_one(
    {
        "email": user.email
    }
)
```

Never trust raw client input as a database query.

---

# 4. Sensitive Data Exposure

Never expose confidential information.

### Bad Response

```json
{
    "id": 10,
    "email": "rahul@gmail.com",
    "password": "$2b$12$abc...",
    "secret_key": "xyz123"
}
```

---

### Good Response

```json
{
    "id": 10,
    "email": "rahul@gmail.com",
    "name": "Rahul"
}
```

---

## Never Return

* Passwords
* Password hashes
* JWT secret
* API keys
* Internal server details
* Database credentials

Use response models to control exactly what is returned.

---

# 5. Mass Assignment

Suppose your model has:

```python
User
```

Fields:

```text
name
email
password
is_admin
```

Client sends:

```json
{
    "name": "Rahul",
    "email": "rahul@gmail.com",
    "password": "pass123",
    "is_admin": true
}
```

If your code blindly saves all fields, the user grants themselves admin privileges.

---

## Safe Practice

Create separate request schemas.

Example:

```python
UserCreate
```

Contains only:

```text
name
email
password
```

Do **not** expose internal fields like `is_admin` in input schemas.

---

# 6. Security Misconfiguration

Examples:

❌ Debug mode enabled in production

```python
debug=True
```

❌ Default passwords

```text
admin
admin123
```

❌ Exposed `.env` files

❌ Missing HTTPS

❌ Missing security headers

---

## Best Practices

* Disable debug mode in production
* Use HTTPS
* Configure CORS correctly
* Keep dependencies updated
* Use environment variables
* Apply security headers

---

# 7. Brute Force Attacks

Attacker tries thousands of passwords.

```text
admin
admin1
admin12
password
password123
qwerty
...
```

Eventually, one might succeed if there are no protections.

---

## Prevention

* Rate limiting
* Account lockout after repeated failures
* CAPTCHA (where appropriate)
* Strong password policy
* Login monitoring and alerts

We'll implement **Rate Limiting** in the next lesson.

---

# 8. File Upload Vulnerabilities

Suppose your API accepts uploads.

Attacker uploads:

```text
virus.exe
```

or

```text
malware.php
```

Without validation, dangerous files could be stored or executed.

---

## Best Practices

Validate:

* File extension
* MIME type
* Maximum file size
* Allowed file types

Example allowed types:

```text
jpg
jpeg
png
pdf
```

Reject everything else unless explicitly required.

---

# 9. Input Validation

Never trust client input.

Bad example:

```text
Age = -200
```

or

```text
Email = abc
```

Use Pydantic validation:

```python
class UserCreate(BaseModel):
    name: str
    email: EmailStr
    password: str
```

Validation should happen before business logic.

---

# 10. Information Leakage

Avoid exposing internal details.

### Bad Error

```json
{
    "error": "MongoDB Authentication Failed
Password=root123"
}
```

This reveals sensitive implementation details.

---

### Better

```json
{
    "detail": "Internal Server Error"
}
```

Log the real error internally, but return a generic message to clients.

---

# Security Checklist

Before deploying your API, verify:

* ✅ Passwords are hashed
* ✅ JWT tokens expire
* ✅ HTTPS is enabled
* ✅ CORS is configured correctly
* ✅ Security headers are present
* ✅ Input validation is implemented
* ✅ Authorization checks are enforced
* ✅ Secrets are stored in environment variables
* ✅ Sensitive data is never returned
* ✅ File uploads are validated
* ✅ Logging is enabled
* ✅ Rate limiting is configured

---

# Real-World Security Layers

```text
                Client
                   │
                   ▼
           HTTPS (TLS)
                   │
                   ▼
          Rate Limiting
                   │
                   ▼
      Security Headers
                   │
                   ▼
        Authentication
                   │
                   ▼
         Authorization
                   │
                   ▼
       Input Validation
                   │
                   ▼
      Business Logic
                   │
                   ▼
          Database
```

Each layer provides additional protection. Security should never rely on a single mechanism.

---

# Interview Questions

### 1. What is the difference between Authentication and Authorization?

* Authentication verifies **who the user is**.
* Authorization determines **what the user is allowed to do**.

---

### 2. What is NoSQL Injection?

It occurs when untrusted input is used directly in a NoSQL database query, allowing an attacker to manipulate the query logic.

---

### 3. Why should passwords never be returned by an API?

Even hashed passwords are sensitive. Exposing them increases the risk of offline cracking and information disclosure.

---

### 4. What is Mass Assignment?

Allowing users to modify fields they should not control (such as `is_admin`) because the application blindly accepts all submitted fields.

---

### 5. How do you protect against Brute Force attacks?

* Rate limiting
* Account lockout
* Strong passwords
* Monitoring and alerting
* CAPTCHA when appropriate

---

# Project Status

Implemented so far:

* ✅ Logging Middleware
* ✅ Request Processing Time Middleware
* ✅ Request ID Middleware
* ✅ Security Headers Middleware
* ✅ CORS Configuration
* ✅ API Security Concepts (OWASP)

---

# Next Lesson

We'll implement **Rate Limiting** in FastAPI using **SlowAPI**, where you'll learn:

* What rate limiting is
* Why APIs need it
* Installing and configuring `slowapi`
* IP-based request limiting
* Custom rate limits per endpoint
* Returning **429 Too Many Requests**
* Production best practices using Redis-backed rate limiting
