Excellent. Since you already know HTML, CSS, and JavaScript basics, we'll build a **large real-world project** and learn API calls properly instead of small examples.

# Project: Job Portal Dashboard

We will build this step by step.

### Features

✅ Search Jobs
✅ Fetch Jobs from Public API
✅ Loading Spinner
✅ Error Handling
✅ Job Details Modal
✅ Pagination
✅ Filters
✅ Save Bookmarks (LocalStorage)
✅ Dark Mode
✅ Async/Await
✅ Fetch API
✅ REST APIs
✅ JSON Handling
✅ Debouncing Search
✅ Infinite Scroll

This project will teach almost everything needed for frontend API integration.

---

# PHASE 1

## Understanding APIs First

Imagine:

Your frontend asks:

```javascript
Give me jobs
```

Backend replies:

```json
[
  {
    "id":1,
    "title":"Frontend Developer",
    "company":"ABC"
  }
]
```

This communication happens using APIs.

---

# Public API We Will Use

We'll use:

```javascript
https://jsonplaceholder.typicode.com/users
```

Why?

Because it is free and perfect for learning.

Later we'll move to real job APIs.

---

# Project Folder

Create:

```text
job-dashboard/
|
|-- index.html
|-- style.css
|-- app.js
```

---

# Step 1: HTML Structure

Open:

```html
index.html
```

Complete code:

```html
<!DOCTYPE html>
<html>

<head>
    <title>Job Dashboard</title>

    <link rel="stylesheet"
          href="style.css">
</head>

<body>

    <div class="container">

        <h1>Job Dashboard</h1>

        <button id="loadBtn">
            Load Users
        </button>

        <div id="loading"></div>

        <div id="usersContainer">

        </div>

    </div>

    <script src="app.js"></script>

</body>

</html>
```

---

# What Each Block Does

### Button

```html
<button id="loadBtn">
```

When clicked:

```javascript
API Call
```

will happen.

---

### Loading Div

```html
<div id="loading"></div>
```

Shows:

```text
Loading...
```

while API data is being fetched.

---

### Users Container

```html
<div id="usersContainer"></div>
```

All API data cards will be inserted here.

---

# Step 2: CSS

Open:

```css
style.css
```

Add:

```css
body{
    font-family: Arial;
    background:#f5f5f5;
}

.container{
    width:80%;
    margin:auto;
}

h1{
    text-align:center;
}

button{
    padding:12px 25px;
    cursor:pointer;
}

.card{
    background:white;
    margin:10px 0;
    padding:15px;
    border-radius:8px;
}
```

---

# Output

You'll see:

```text
Job Dashboard

[ Load Users ]
```

---

# Step 3: JavaScript

Open:

```javascript
app.js
```

---

## Select Elements

Add:

```javascript
const loadBtn =
document.getElementById("loadBtn");

const usersContainer =
document.getElementById("usersContainer");

const loading =
document.getElementById("loading");
```

---

# Why?

These variables connect JavaScript with HTML.

Example:

```javascript
loading.innerHTML="Loading...";
```

updates:

```html
<div id="loading"></div>
```

---

# Step 4: First API Call

Add:

```javascript
loadBtn.addEventListener(
    "click",
    getUsers
);
```

---

## What Happens?

When user clicks:

```text
Load Users
```

JavaScript calls:

```javascript
getUsers()
```

---

# Create Function

Add below:

```javascript
async function getUsers(){

}
```

---

# Why async?

Because API takes time.

JavaScript should wait.

Example:

```javascript
const data =
await response.json();
```

Without async:

```javascript
await
```

cannot be used.

---

# Step 5: Show Loading Message

Inside:

```javascript
async function getUsers()
```

Add:

```javascript
loading.innerHTML =
"Loading...";
```

Now:

```text
Loading...
```

appears while data is being fetched.

---

# Step 6: Fetch API

Add:

```javascript
const response =
await fetch(
"https://jsonplaceholder.typicode.com/users"
);
```

Full function:

```javascript
async function getUsers(){

    loading.innerHTML =
    "Loading...";

    const response =
    await fetch(
    "https://jsonplaceholder.typicode.com/users"
    );

}
```

---

# Understanding fetch()

This line:

```javascript
fetch(url)
```

sends HTTP request.

Browser asks server:

```text
Give me users
```

Server responds:

```json
[
  {...},
  {...}
]
```

---

# Step 7: Convert JSON

Below response:

```javascript
const data =
await response.json();
```

Now:

```javascript
console.log(data);
```

Complete:

```javascript
async function getUsers(){

    loading.innerHTML =
    "Loading...";

    const response =
    await fetch(
    "https://jsonplaceholder.typicode.com/users"
    );

    const data =
    await response.json();

    console.log(data);
}
```

---

# What response.json() Does

Server sends:

```json
[
 {
   "id":1,
   "name":"Leanne Graham"
 }
]
```

JavaScript converts it into:

```javascript
[
 {
   id:1,
   name:"Leanne Graham"
 }
]
```

usable objects.

---

# Step 8: Check Browser Console

Click:

```text
Load Users
```

Open:

```text
F12
```

Console:

```javascript
(10) [{...}, {...}]
```

You successfully called your first API.

---

# Step 9: Display Data on Screen

After:

```javascript
console.log(data);
```

add:

```javascript
usersContainer.innerHTML="";
```

---

# Loop Through Users

Add:

```javascript
data.forEach(user=>{

});
```

---

Inside:

```javascript
data.forEach(user=>{

});
```

Add:

```javascript
usersContainer.innerHTML += `
<div class="card">

<h3>${user.name}</h3>

<p>${user.email}</p>

<p>${user.phone}</p>

</div>
`;
```

---

Complete Function

```javascript
async function getUsers(){

    loading.innerHTML =
    "Loading...";

    const response =
    await fetch(
    "https://jsonplaceholder.typicode.com/users"
    );

    const data =
    await response.json();

    usersContainer.innerHTML="";

    data.forEach(user=>{

        usersContainer.innerHTML += `
        <div class="card">

            <h3>${user.name}</h3>

            <p>${user.email}</p>

            <p>${user.phone}</p>

        </div>
        `;

    });

}
```

---

# Step 10: Remove Loading

After loop:

```javascript
loading.innerHTML="";
```

Final:

```javascript
async function getUsers(){

    loading.innerHTML =
    "Loading...";

    const response =
    await fetch(
    "https://jsonplaceholder.typicode.com/users"
    );

    const data =
    await response.json();

    usersContainer.innerHTML="";

    data.forEach(user=>{

        usersContainer.innerHTML += `
        <div class="card">

            <h3>${user.name}</h3>

            <p>${user.email}</p>

            <p>${user.phone}</p>

        </div>
        `;

    });

    loading.innerHTML="";
}
```

---

# Data Flow Visualization

```text
Button Click
     ↓
getUsers()
     ↓
fetch()
     ↓
Server
     ↓
JSON Response
     ↓
response.json()
     ↓
JavaScript Object
     ↓
forEach()
     ↓
HTML Cards
     ↓
Display on Screen
```

---

# What You Learned Today

### HTML

```html
button
div
```

### CSS

```css
card design
spacing
layout
```

### JavaScript

```javascript
addEventListener()
async
await
fetch()
response.json()
forEach()
innerHTML
template literals
```

### API Concepts

```javascript
Request
Response
JSON
REST API
HTTP
```

---

### Homework

Add another field:

```html
Website
```

using:

```javascript
user.website
```

inside each card.

In the next response, we will make this project **professional** by adding:

1. `try...catch`
2. Error Handling
3. HTTP Status Checking
4. Search Users
5. Dynamic User Details Page
6. Reusable API Functions
7. Understanding every line of the Fetch Lifecycle in depth.
