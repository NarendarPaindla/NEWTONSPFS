# Request Body Basics in FastAPI

Till now we learned:

```text
GET Requests
Routes
Path Parameters
Query Parameters
```

These are useful when the client wants to **retrieve data**.

But what if the client wants to **send data to the server**?

Examples:

```text
Student Registration
User Signup
Login
Create Product
Place Order
```

For this, we use:

# Request Body

---

# Real Life Example

Imagine a college admission form.

Student enters:

```text
Name   : Ravi
Age    : 21
Branch : CSE
```

Then clicks:

```text
Submit
```

The form data travels from browser to server.

That data is called:

```text
Request Body
```

---

# What is a Request Body?

A Request Body is data sent by the client to the server inside an HTTP request.

Visual:

```text
Client
   |
   | Request Body
   |
   V
FastAPI
```

---

# Real Industry Examples

### User Registration

```json
{
  "name": "Ravi",
  "email": "ravi@gmail.com",
  "password": "123456"
}
```

---

### Login

```json
{
  "username": "ravi",
  "password": "123456"
}
```

---

### Product Creation

```json
{
  "name": "Laptop",
  "price": 50000
}
```

All these JSON objects are Request Bodies.

---

# Why Not Use Query Parameters?

Suppose we do:

```http
/signup?
name=Ravi&
email=ravi@gmail.com&
password=123456
```

Problems:

❌ URL becomes huge

❌ Password visible

❌ Hard to manage

❌ Not professional

---

Instead:

```http
POST /signup
```

Request Body:

```json
{
  "name":"Ravi",
  "email":"ravi@gmail.com",
  "password":"123456"
}
```

Much better.

---

# First Request Body Example

```python
from fastapi import FastAPI

app = FastAPI()

@app.post("/students")
def create_student(student: dict):
    return student
```

---

# What Happens?

Client sends:

```json
{
  "name":"Ravi",
  "age":21
}
```

FastAPI receives:

```python
{
  "name":"Ravi",
  "age":21
}
```

and returns it.

---

# Test in Swagger

Open:

```text
http://127.0.0.1:8000/docs
```

You will see:

```text
POST /students
```

Click:

```text
Try It Out
```

Enter:

```json
{
  "name":"Ravi",
  "age":21
}
```

Execute.

Response:

```json
{
  "name":"Ravi",
  "age":21
}
```

---

# Understanding the Flow

```text
Frontend
   |
   | JSON
   |
   V
FastAPI
   |
   | Python Dictionary
   |
   V
Function
   |
   V
Response
```

---

# Real Student Management Example

```python
@app.post("/students")
def create_student(student: dict):

    return {
        "message": "Student Created",
        "data": student
    }
```

---

Request:

```json
{
  "name":"Ravi",
  "age":21,
  "course":"CSE"
}
```

---

Response:

```json
{
  "message":"Student Created",
  "data":{
      "name":"Ravi",
      "age":21,
      "course":"CSE"
  }
}
```

---

# How Does FastAPI Know It's a Request Body?

Look:

```python
student: dict
```

This is neither:

```python
/student/{student}
```

(Path Parameter)

nor

```python
?student=value
```

(Query Parameter)

So FastAPI automatically assumes:

```text
Request Body
```

---

# POST Requests Usually Use Request Body

Most common combination:

```http
POST + Request Body
```

Example:

```http
POST /students
```

Body:

```json
{
  "name":"Ravi"
}
```

---

# Request Body vs Query Parameter

---

## Query Parameter

```http
/students?department=CSE
```

Used for:

```text
Filtering
Searching
Sorting
Pagination
```

---

## Request Body

```json
{
  "name":"Ravi",
  "age":21
}
```

Used for:

```text
Creating Data
Updating Data
Submitting Forms
```

---

# Real Industry Example

### Registration API

```http
POST /register
```

Request Body:

```json
{
  "username":"ravi",
  "email":"ravi@gmail.com",
  "password":"123456"
}
```

---

### Login API

```http
POST /login
```

Request Body:

```json
{
  "username":"ravi",
  "password":"123456"
}
```

---

### Create Product

```http
POST /products
```

Request Body:

```json
{
  "name":"iPhone",
  "price":70000
}
```

---

# Problem with Dictionary Approach

Suppose client sends:

```json
{
  "name":"Ravi"
}
```

Age missing.

FastAPI accepts it.

---

Suppose client sends:

```json
{
  "name":"Ravi",
  "age":"twenty one"
}
```

FastAPI still accepts it.

Dangerous.

---

Why?

Because:

```python
student: dict
```

means:

```text
Accept Anything
```

No validation.

---

# Industry Problem

Imagine Banking API.

Request:

```json
{
  "amount":"fifty thousand"
}
```

Should server accept?

❌ No

Need validation.

---

# Enter Pydantic

This is where FastAPI becomes powerful.

Instead of:

```python
student: dict
```

we create:

```python
class Student(BaseModel):
    name: str
    age: int
```

Now FastAPI validates automatically.

We'll learn this in the next topic.

---

# Request Body Lifecycle

Suppose client sends:

```json
{
  "name":"Ravi",
  "age":21
}
```

---

Step 1

Client sends JSON.

---

Step 2

FastAPI receives request.

---

Step 3

JSON is converted to Python.

```python
{
  "name":"Ravi",
  "age":21
}
```

This is:

```text
Deserialization
```

---

Step 4

Function executes.

---

Step 5

Response generated.

---

Step 6

Python object converted back to JSON.

```text
Serialization
```

---

Step 7

Client receives response.

---

# Complete Flow

```text
Client
   |
JSON Request Body
   |
   V
FastAPI
   |
Deserialization
   |
Python Object
   |
Business Logic
   |
Python Response
   |
Serialization
   |
JSON Response
   |
Client
```

---

# Hands-On Exercise

Create:

```python
from fastapi import FastAPI

app = FastAPI()

@app.post("/employee")
def create_employee(employee: dict):

    return {
        "message": "Employee Created",
        "employee": employee
    }
```

---

Request:

```json
{
  "name":"Kiran",
  "salary":50000
}
```

---

Response:

```json
{
  "message":"Employee Created",
  "employee":{
      "name":"Kiran",
      "salary":50000
  }
}
```

---

# Common Beginner Mistakes

### Mistake 1

Using GET for Request Body

```python
@app.get("/students")
```

Not recommended.

Request bodies are mainly used with:

```text
POST
PUT
PATCH
```

---

### Mistake 2

Sending Invalid JSON

Wrong:

```json
{
 name:"Ravi"
}
```

Keys must be quoted.

---

Correct:

```json
{
 "name":"Ravi"
}
```

---

### Mistake 3

Expecting Validation with dict

```python
student: dict
```

No validation.

Use Pydantic later.

---

# Interview Questions

### What is a Request Body?

Data sent from the client to the server as part of an HTTP request.

---

### Which HTTP methods commonly use Request Bodies?

```text
POST
PUT
PATCH
```

---

### What format is most commonly used?

```text
JSON
```

---

### How does FastAPI receive Request Body data?

It converts JSON into Python objects automatically.

---

### Why is `dict` not ideal for Request Bodies?

Because it provides no validation.

---

# Golden Rule

Use:

```text
Path Parameters
```

for:

```text
Identifying Resources
```

Example:

```http
/students/101
```

---

Use:

```text
Query Parameters
```

for:

```text
Filtering/Search/Pagination
```

Example:

```http
/students?department=CSE
```

---

Use:

```text
Request Body
```

for:

```text
Sending Data
Creating Resources
Updating Resources
```

Example:

```json
{
  "name":"Ravi",
  "age":21
}
```

This is the foundation of every real-world FastAPI application.

