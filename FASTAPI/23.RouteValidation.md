# Route Validation in FastAPI

One of FastAPI's biggest superpowers is:

> **Automatic Validation**

In many frameworks, developers manually write validation code.

In FastAPI:

```python
student_id: int
```

and FastAPI validates automatically.

---

# Why Validation is Important?

Imagine a Banking API.

Expected:

```http
/accounts/12345
```

But user sends:

```http
/accounts/hello
```

Should the application continue?

❌ No

Because:

```text
Account Number must be numeric
```

Validation protects our application.

---

# What is Route Validation?

Route Validation means:

> FastAPI automatically checks whether incoming path parameters, query parameters, and request body data match the expected type and rules.

---

# Path Parameter Validation

Example:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/students/{student_id}")
def get_student(student_id: int):
    return {
        "student_id": student_id
    }
```

---

# Valid Request

```http
GET /students/101
```

Response:

```json
{
  "student_id": 101
}
```

---

# Invalid Request

```http
GET /students/abc
```

FastAPI tries:

```python
int("abc")
```

Impossible.

Response:

```http
422 Unprocessable Entity
```

```json
{
  "detail":[
    {
      "type":"int_parsing",
      "loc":["path","student_id"]
    }
  ]
}
```

---

# How FastAPI Works Internally

Request:

```http
/students/101
```

Flow:

```text
Receive Request
      ↓
Extract student_id
      ↓
Convert to int
      ↓
Success
      ↓
Execute Function
```

---

Invalid:

```http
/students/abc
```

Flow:

```text
Receive Request
      ↓
Convert to int
      ↓
Failed
      ↓
422 Response
```

Function never executes.

---

# Query Parameter Validation

Example:

```python
@app.get("/students")
def students(page: int):
    return {
        "page": page
    }
```

---

Valid:

```http
/students?page=2
```

Response:

```json
{
  "page":2
}
```

---

Invalid:

```http
/students?page=abc
```

Response:

```http
422
```

Because:

```text
abc is not an integer
```

---

# Automatic Type Conversion

Request:

```http
/students?page=5
```

URL values are always strings.

FastAPI automatically converts:

```text
"5"
 ↓
5
```

because:

```python
page: int
```

was specified.

---

# String Validation

Example:

```python
@app.get("/users/{username}")
def user(username: str):
    return {
        "username": username
    }
```

Valid:

```http
/users/ravi
```

Response:

```json
{
  "username":"ravi"
}
```

---

# Float Validation

Example:

```python
@app.get("/products")
def products(price: float):
    return {
        "price": price
    }
```

Request:

```http
/products?price=49999.99
```

Response:

```json
{
  "price":49999.99
}
```

---

Invalid:

```http
/products?price=abc
```

Response:

```http
422
```

---

# Boolean Validation

Example:

```python
@app.get("/students")
def students(active: bool):
    return {
        "active": active
    }
```

---

Request:

```http
/students?active=true
```

Response:

```json
{
  "active": true
}
```

---

Request:

```http
/students?active=false
```

Response:

```json
{
  "active": false
}
```

---

FastAPI converts automatically.

---

# Validation Using Path()

FastAPI provides additional validation.

Import:

```python
from fastapi import Path
```

---

Example:

```python
from fastapi import FastAPI, Path

app = FastAPI()

@app.get("/students/{student_id}")
def get_student(
    student_id: int = Path(gt=0)
):
    return {
        "student_id": student_id
    }
```

---

Meaning:

```text
student_id > 0
```

must be true.

---

Valid:

```http
/students/10
```

---

Invalid:

```http
/students/0
```

Response:

```http
422
```

---

# More Path Validation Rules

## Greater Than

```python
student_id: int = Path(gt=0)
```

```text
1,2,3...
```

allowed.

---

## Greater Than or Equal

```python
student_id: int = Path(ge=1)
```

```text
1,2,3...
```

allowed.

---

## Less Than

```python
student_id: int = Path(lt=100)
```

Only:

```text
0 - 99
```

allowed.

---

## Less Than or Equal

```python
student_id: int = Path(le=100)
```

Maximum:

```text
100
```

---

# Real Example

College IDs:

```python
@app.get("/students/{student_id}")
def get_student(
    student_id: int = Path(
        ge=1,
        le=5000
    )
):
    return {
        "student_id": student_id
    }
```

---

Valid:

```http
/students/2500
```

---

Invalid:

```http
/students/7000
```

FastAPI returns:

```http
422
```

---

# Validation Using Query()

Import:

```python
from fastapi import Query
```

---

Example:

```python
from fastapi import Query

@app.get("/students")
def students(
    page: int = Query(
        default=1,
        ge=1
    )
):
    return {
        "page": page
    }
```

---

Valid:

```http
/students?page=5
```

---

Invalid:

```http
/students?page=0
```

Response:

```http
422
```

---

# String Length Validation

Example:

```python
from fastapi import Query

@app.get("/search")
def search(
    keyword: str = Query(
        min_length=3,
        max_length=20
    )
):
    return {
        "keyword": keyword
    }
```

---

Valid:

```http
/search?keyword=python
```

---

Invalid:

```http
/search?keyword=ab
```

Too short.

FastAPI rejects.

---

# Real Industry Example

Product Search API

```python
from fastapi import FastAPI, Query

app = FastAPI()

@app.get("/products")
def products(
    search: str = Query(
        min_length=2,
        max_length=50
    ),
    page: int = Query(
        default=1,
        ge=1
    )
):
    return {
        "search": search,
        "page": page
    }
```

---

Request:

```http
/products?search=laptop&page=2
```

Response:

```json
{
  "search":"laptop",
  "page":2
}
```

---

# Swagger Integration

One amazing thing:

Open:

```text
/docs
```

FastAPI automatically shows:

```text
Minimum Length
Maximum Length
Required Fields
Default Values
```

No extra documentation needed.

---

# Benefits of Route Validation

### Security

```text
Invalid Data Rejected
```

---

### Cleaner Code

No manual checks.

---

### Better APIs

Clients immediately know what's wrong.

---

### Automatic Documentation

Swagger updates automatically.

---

# Common Beginner Mistakes

## Wrong

```python
@app.get("/students/{student_id}")
def get_student(student_id):
```

No validation.

---

Correct:

```python
@app.get("/students/{student_id}")
def get_student(student_id: int):
```

---

## Wrong

Manual validation:

```python
if page < 1:
    return "Error"
```

---

Better:

```python
page: int = Query(ge=1)
```

Let FastAPI handle it.

---

# Interview Questions

### What is Route Validation?

Automatic checking of route parameters against expected types and constraints.

---

### What status code does FastAPI return for validation failures?

```http
422 Unprocessable Entity
```

---

### Which functions provide advanced validation?

```python
Path()
Query()
```

---

### Can FastAPI automatically convert types?

Yes.

Example:

```text
"10"
 ↓
10
```

for:

```python
student_id: int
```

---

# Golden Rule

Whenever you write:

```python
student_id: int
```

FastAPI automatically:

```text
Extracts Value
      ↓
Validates Type
      ↓
Converts Data
      ↓
Returns 422 if Invalid
```

That's one of the reasons FastAPI is loved in industry—**validation is built into the framework instead of being manually written everywhere.**

---

This is where FastAPI's validation system becomes truly powerful because entire JSON request bodies can be validated automatically.
