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
Perfect. Before we build projects, we need to completely master:

# Fetch API + async/await + GET Requests + HTTP Status Codes + Error Handling

This is where many students memorize code but don't understand what is happening.

---

# Revisiting Fetch

We learned:

```javascript id="j9jkvq"
fetch(url)
```

returns:

```javascript id="v89ub4"
Promise
```

Example:

```javascript id="x3n4dn"
const result =
fetch("https://jsonplaceholder.typicode.com/users");

console.log(result);
```

Output:

```javascript id="8pq7x0"
Promise { <pending> }
```

Because the request is still happening.

---

# Fetch Using async/await

Promise Version:

```javascript id="s8rfdq"
fetch(url)
.then(response => {
    return response.json();
})
.then(data => {
    console.log(data);
});
```

---

Modern Version:

```javascript id="by7s2i"
async function getUsers() {

    const response =
    await fetch(url);

    const data =
    await response.json();

    console.log(data);

}
```

Much easier to read.

---

# Let's Understand Every Line

```javascript id="b4d0ck"
const response =
await fetch(url);
```

Meaning:

```text id="f9m00v"
Send Request
     ↓
Wait For Response
     ↓
Store Response Object
```

---

Example:

```javascript id="g4m7r6"
const response =
await fetch(
"https://jsonplaceholder.typicode.com/users"
);
```

After request completes:

```javascript id="c8gkmi"
response
```

contains:

```javascript id="7tb4i9"
Response {
   ok:true,
   status:200,
   ...
}
```

Still NOT actual users.

---

# Next Step

```javascript id="ekwrq0"
const data =
await response.json();
```

Meaning:

```text id="3kbbul"
Take JSON Response
      ↓
Convert To JS Object
      ↓
Store In data
```

---

Now:

```javascript id="24xg7v"
console.log(data);
```

Output:

```javascript id="hd0f3z"
[
  {
    id:1,
    name:"Leanne Graham"
  },
  ...
]
```

---

# Complete Example

```javascript id="i7n3c5"
async function getUsers() {

    const response =
    await fetch(
      "https://jsonplaceholder.typicode.com/users"
    );

    const users =
    await response.json();

    console.log(users);

}

getUsers();
```

---

# Visual Flow

```text id="kz8dvy"
fetch()
   ↓
Response Object
   ↓
response.json()
   ↓
JavaScript Objects
   ↓
Use Data
```

---

# What is GET Request?

When you want data from server:

```text id="w6g1m7"
Get Users

Get Products

Get Orders

Get Movies
```

You use:

```text id="ffrz6g"
GET
```

---

Example:

```javascript id="n0hizx"
fetch(
"https://jsonplaceholder.typicode.com/users"
);
```

By default:

```javascript id="x2g6va"
fetch()
```

uses:

```text id="pddxzf"
GET
```

---

# Explicit GET Request

```javascript id="f9kkli"
fetch(url,{
    method:"GET"
});
```

Same result.

---

# Real API Response Example

Suppose server returns:

```json id="g8wubf"
{
   "id":1,
   "name":"Narendar",
   "email":"narendar@gmail.com"
}
```

After:

```javascript id="42yb20"
await response.json();
```

You get:

```javascript id="hndjlwm"
{
   id:1,
   name:"Narendar",
   email:"narendar@gmail.com"
}
```

A real JavaScript object.

---

# Accessing Data

Example:

```javascript id="uy0nhc"
const user =
await response.json();

console.log(user.name);
```

Output:

```javascript id="am5vfr"
Narendar
```

---

# Array Responses

Many APIs return arrays.

Example:

```javascript id="wryj0v"
const users =
await response.json();
```

Output:

```javascript id="5oc07p"
[
 {
   id:1,
   name:"A"
 },
 {
   id:2,
   name:"B"
 }
]
```

---

# Loop Through Data

```javascript id="ijh2ce"
users.forEach(user => {

    console.log(user.name);

});
```

Output:

```javascript id="ngc1xa"
A
B
```

---

# HTTP Status Codes

Very Important.

Whenever server responds:

```text id="nl7n1x"
Success ?
Failure ?
Permission Denied ?
Page Missing ?
```

Status code tells us.

---

# 200 OK

Most common.

```javascript id="nqpd6m"
status:200
```

Meaning:

```text id="nr7w6k"
Success
```

---

# 201 Created

```javascript id="z74mh9"
status:201
```

Meaning:

```text id="3xjlju"
Resource Created
```

Usually after POST requests.

---

# 400 Bad Request

```javascript id="q4t0b4"
status:400
```

Meaning:

```text id="d0p2lx"
Client Sent Wrong Data
```

Example:

```text id="3p1xxn"
Missing username
Invalid email
```

---

# 401 Unauthorized

```javascript id="73h06i"
status:401
```

Meaning:

```text id="ajf7hm"
Login Required
```

---

# 403 Forbidden

```javascript id="n4czci"
status:403
```

Meaning:

```text id="z9nbcm"
Access Denied
```

---

# 404 Not Found

Most famous.

```javascript id="s7c3w6"
status:404
```

Meaning:

```text id="w36f9u"
Resource Not Found
```

Example:

```text id="jddq0z"
User doesn't exist
Page doesn't exist
```

---

# 500 Internal Server Error

```javascript id="f0k78m"
status:500
```

Meaning:

```text id="5x9r2x"
Server Crashed
```

Backend problem.

---

# Checking Status

Example:

```javascript id="8v6pgc"
const response =
await fetch(url);

console.log(response.status);
```

Output:

```javascript id="f8x2ch"
200
```

---

# response.ok

Very useful.

Example:

```javascript id="9dlahv"
console.log(response.ok);
```

Output:

```javascript id="m7fqrv"
true
```

For success.

---

# Error Handling Problem

Many students think:

```javascript id="grvdl4"
try {
   const response =
   await fetch(url);
}
catch(error){
}
```

handles all API errors.

Not true.

---

# Important Interview Question

Suppose:

```javascript id="12ulwn"
https://api.com/users/999
```

returns:

```javascript id="l94j6z"
404
```

Will fetch throw an error?

Answer:

```text id="r8i9f4"
NO
```

This surprises many developers.

---

# Example

```javascript id="zsf1s0"
const response =
await fetch(badUrl);

console.log(response.status);
```

Output:

```javascript id="hl70x0"
404
```

No exception thrown.

---

# Why?

Because request succeeded.

Server responded correctly.

Response just happened to be:

```javascript id="g1ql4g"
404
```

---

# Correct Error Handling

```javascript id="2pfxwh"
async function getUsers() {

    try {

        const response =
        await fetch(url);

        if(!response.ok){

            throw new Error(
                "Request Failed"
            );

        }

        const data =
        await response.json();

        console.log(data);

    }
    catch(error){

        console.log(error.message);

    }

}
```

---

# Professional Pattern

This pattern is used everywhere.

```javascript id="7t4kgj"
try {

    const response =
    await fetch(url);

    if(!response.ok){

        throw new Error(
            `HTTP Error ${response.status}`
        );

    }

    const data =
    await response.json();

}
catch(error){

    console.log(error);

}
```

Remember it.

You'll use it hundreds of times.

---

# Network Error Example

Suppose internet is disconnected.

```text id="vwcns7"
No Internet
```

Now:

```javascript id="u67ydz"
await fetch(url);
```

fails completely.

This time:

```javascript id="ynp1ya"
catch()
```

runs automatically.

```javascript id="35kkrk"
try {

    await fetch(url);

}
catch(error){

    console.log(
        "Internet Problem"
    );

}
```

---

# Building First Real Fetch Function

```javascript id="8l55di"
async function getUsers() {

    try {

        const response =
        await fetch(
            "https://jsonplaceholder.typicode.com/users"
        );

        if(!response.ok){

            throw new Error(
                "Failed To Fetch Users"
            );

        }

        const users =
        await response.json();

        console.log(users);

    }
    catch(error){

        console.log(error.message);

    }

}

getUsers();
```

This is already close to production-quality code.

---

# Practice Questions

### Q1

What does fetch return?

```javascript id="tq7e5r"
fetch(url);
```

Answer:

```javascript id="xryphz"
Promise
```

---

### Q2

What does response.json() return?

Answer:

```javascript id="trtxjn"
Promise
```

---

### Q3

Will fetch throw error for 404?

Answer:

```text id="b6v72z"
No
```

Must manually check:

```javascript id="7n3fny"
response.ok
```

or

```javascript id="lw6wxg"
response.status
```

---

### Q4

Most common status codes?

```text id="lg1g67"
200 OK
201 Created
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
500 Internal Server Error
```

---
---


