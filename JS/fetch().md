Excellent. Now we start the most important practical topic in modern JavaScript:

# Fetch API + Working with JSON (Part 1)

Do not rush this topic.

Many developers memorize:

```javascript
fetch()
.then()
await
response.json()
```

but they don't understand:

* What fetch actually does
* What JSON actually is
* What Response object is
* Why `response.json()` is needed
* How browser communicates with server

We will understand everything deeply.

---

# Before Fetch API

Let's understand the actual problem.

Imagine your website needs:

```text
User Profile
Products
Orders
Comments
Weather Data
Movies Data
```

Where is this data stored?

Not inside JavaScript.

Usually:

```text
Database
   ↓
Backend Server
   ↓
API
   ↓
Frontend
```

Example:

```text
Browser
   ↓
Request User Data
   ↓
Server
   ↓
Response User Data
```

This communication happens through:

```text
HTTP Requests
```

---

# Real World Example

Suppose you open Instagram.

Browser asks:

```text
Give me my profile
```

Server responds:

```text
{
  "username":"narendar",
  "followers":1000
}
```

Browser displays it.

---

# What is an API?

API stands for:

```text
Application Programming Interface
```

Simple meaning:

> A way for one software application to talk to another software application.

Example:

```text
Frontend
     ↓
API
     ↓
Backend
```

---

# Example

Frontend requests:

```text
GET /users/1
```

Backend responds:

```json
{
  "id":1,
  "name":"Narendar"
}
```

---

# What is Fetch API?

Fetch API is a browser-provided function used to make HTTP requests.

Syntax:

```javascript
fetch(url);
```

Example:

```javascript
fetch("https://example.com/users");
```

Meaning:

```text
Browser
   ↓
Send Request
   ↓
Wait for Response
   ↓
Receive Data
```

---

# Important Question

Does fetch return actual data?

Many beginners think:

```javascript
const data = fetch(url);
```

means:

```javascript
data = actual response
```

Wrong.

Fetch returns:

```javascript
Promise
```

---

# Proof

```javascript
const result =
fetch("https://example.com");

console.log(result);
```

Output:

```javascript
Promise { <pending> }
```

Because network requests take time.

---

# Why Promise?

Imagine:

```text
Internet Slow
```

Request may take:

```text
1 second
5 seconds
10 seconds
```

JavaScript cannot freeze.

Therefore:

```javascript
fetch()
```

returns a Promise immediately.

---

# Basic Fetch Example

```javascript
fetch("https://jsonplaceholder.typicode.com/users")
.then(response => {
    console.log(response);
});
```

Output:

```javascript
Response { ... }
```

Notice:

Not actual user data.

You receive a:

```javascript
Response Object
```

---

# Understanding Response Object

This is extremely important.

Many students get confused.

When server responds:

```text
Server
  ↓
Users Data
```

Browser wraps it into:

```javascript
Response Object
```

Example:

```javascript
fetch(url)
.then(response => {
    console.log(response);
});
```

You may see:

```javascript
Response
{
  ok:true,
  status:200,
  statusText:"OK",
  ...
}
```

---

# Important Properties

## response.status

HTTP status code.

```javascript
console.log(response.status);
```

Example:

```javascript
200
```

Meaning:

```text
Success
```

---

## response.ok

Returns:

```javascript
true
```

or

```javascript
false
```

Example:

```javascript
console.log(response.ok);
```

Output:

```javascript
true
```

---

## response.statusText

```javascript
console.log(response.statusText);
```

Output:

```javascript
OK
```

---

# Why We Don't Get Actual Data?

Because server sends data as:

```text
JSON String
```

Not JavaScript Object.

Therefore browser gives:

```javascript
Response Object
```

first.

Then we extract data.

---

# What is JSON?

This topic is critical.

JSON means:

```text
JavaScript Object Notation
```

It is the most common data format used in APIs.

---

# JavaScript Object

```javascript
const user = {
    id:1,
    name:"Narendar"
};
```

---

# JSON Version

```json
{
  "id":1,
  "name":"Narendar"
}
```

Notice difference.

---

# JavaScript Object

Keys can be:

```javascript
{
    id:1
}
```

---

# JSON

Keys must be in quotes:

```json
{
   "id":1
}
```

---

# JavaScript Object Supports

```javascript
{
   name:"Narendar",
   age:25,
   isActive:true
}
```

JSON supports same values but follows stricter formatting.

---

# Why JSON?

Because every language understands it.

```text
Java
Python
NodeJS
PHP
C#
Go
React
Angular
```

All can exchange data through JSON.

---

# Example API Response

```json
{
  "id":1,
  "name":"Leanne Graham",
  "email":"leanne@example.com"
}
```

Server sends this.

Browser receives it.

---

# Converting JSON to JavaScript Object

JavaScript provides:

```javascript
JSON.parse()
```

Example:

```javascript
const jsonString =
'{"name":"Narendar"}';

const obj =
JSON.parse(jsonString);

console.log(obj);
```

Output:

```javascript
{
 name:"Narendar"
}
```

---

# Converting Object to JSON

JavaScript provides:

```javascript
JSON.stringify()
```

Example:

```javascript
const user = {
    name:"Narendar"
};

const json =
JSON.stringify(user);

console.log(json);
```

Output:

```javascript
{"name":"Narendar"}
```

---

# Why response.json() Exists

This is one of the most important interview questions.

Server sends:

```json
{
  "id":1,
  "name":"Narendar"
}
```

Browser receives:

```javascript
Response Object
```

To extract JSON:

```javascript
response.json()
```

Example:

```javascript
fetch(url)
.then(response => {
    return response.json();
});
```

This converts:

```text
JSON Data
    ↓
JavaScript Object
```

---

# Complete Flow

```text
fetch()
   ↓
Promise
   ↓
Response Object
   ↓
response.json()
   ↓
JavaScript Object
   ↓
Use Data
```

---

# First Complete Fetch Example

```javascript
fetch(
"https://jsonplaceholder.typicode.com/users/1"
)
.then(response => {

    return response.json();

})
.then(data => {

    console.log(data);

});
```

Output:

```javascript
{
 id:1,
 name:"Leanne Graham",
 username:"Bret",
 ...
}
```

---

# Visual Representation

```text
Request Sent
      ↓
Server Response
      ↓
Response Object
      ↓
response.json()
      ↓
JavaScript Object
      ↓
Display Data
```

---

# Very Important Interview Question

What does:

```javascript
response.json()
```

return?

Many students answer:

```javascript
Object
```

Wrong.

It returns:

```javascript
Promise
```

Because parsing may take time.

That's why:

```javascript
fetch(url)
.then(response => {
    return response.json();
})
.then(data => {
    console.log(data);
});
```

works.

---

# Practice

### Q1

What does fetch return?

```javascript
fetch(url);
```

Answer:

```javascript
Promise
```

---

### Q2

What does response.json() return?

Answer:

```javascript
Promise
```

---

### Q3

What is JSON?

Answer:

```text
JavaScript Object Notation
```

used for data exchange between systems.

---
se.json()
```

In the next part, we'll go deep into **Fetch API with async/await, GET Requests, Response Status Codes (200, 404, 500), and robust error handling**, which forms the foundation of every React application.
