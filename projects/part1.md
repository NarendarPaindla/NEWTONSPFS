
Excellent. Instead of showing small API examples, let's build a **real-world IMDb-style Movie Search Application** from scratch using:

* HTML
* CSS
* JavaScript
* Public Movie API (OMDb API)
* Fetch API
* Dynamic DOM Manipulation
* Loading States
* Error Handling
* Search Feature
* Movie Details Page
* Pagination
* Favorites (LocalStorage)
* Responsive Design

This project will teach almost everything a frontend developer needs regarding API calls.

---

# Project We Will Build

```
MovieVerse
```

Features:

✅ Search Movies

✅ Display Movie Cards

✅ View Full Movie Details

✅ Loading Spinner

✅ Error Messages

✅ Pagination

✅ Save Favorites

✅ Responsive UI

✅ API Integration

✅ Local Storage

---

# FINAL PROJECT STRUCTURE

```text
movieverse/
│
├── index.html
├── style.css
├── script.js
│
├── images/
│
└── pages/
    └── movie.html
```

---

# STEP 1 — Understanding API

First understand:

Frontend cannot magically know movie data.

We request it from an API.

Example:

```javascript
fetch("https://www.omdbapi.com/?apikey=YOUR_API_KEY&s=batman")
```

Response:

```json
{
  "Search":[
    {
      "Title":"Batman Begins",
      "Year":"2005",
      "Poster":"..."
    }
  ]
}
```

Flow:

```text
User Searches Movie
       ↓
JavaScript fetch()
       ↓
API Server
       ↓
JSON Response
       ↓
Display on Screen
```

---

# STEP 2 — Get Free API Key

Go to:

```text
https://www.omdbapi.com/apikey.aspx
```

Get free API key.

Example:

```text
abcd1234
```

Replace everywhere:

```javascript
YOUR_API_KEY
```

with

```javascript
abcd1234
```

---

# STEP 3 — Create HTML Skeleton

Create:

```html
index.html
```

Paste:

```html
<!DOCTYPE html>
<html>

<head>

<meta charset="UTF-8">

<meta
name="viewport"
content="width=device-width, initial-scale=1.0">

<title>MovieVerse</title>

<link rel="stylesheet" href="style.css">

</head>

<body>

<header>

<h1>MovieVerse</h1>

<div class="search-box">

<input
type="text"
id="searchInput"
placeholder="Search Movies">

<button id="searchBtn">
Search
</button>

</div>

</header>

<main>

<div id="moviesContainer">

</div>

</main>

<script src="script.js"></script>

</body>
</html>
```

---

# Understanding Each Line

Header:

```html
<header>
```

Top section of website.

---

Input:

```html
<input
id="searchInput">
```

User types movie name.

---

Button:

```html
<button id="searchBtn">
```

Triggers search.

---

Container:

```html
<div id="moviesContainer">
```

Movie cards inserted here dynamically.

---

# STEP 4 — Add CSS

Create:

```css
style.css
```

Paste:

```css
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:Arial;
}

body{
background:#111;
color:white;
min-height:100vh;
}

header{
padding:20px;
text-align:center;
}

h1{
margin-bottom:20px;
}

.search-box{
display:flex;
justify-content:center;
gap:10px;
}

input{
width:300px;
padding:10px;
border:none;
outline:none;
}

button{
padding:10px 20px;
cursor:pointer;
}

#moviesContainer{
display:grid;
grid-template-columns:
repeat(auto-fit,minmax(220px,1fr));

gap:20px;

padding:20px;
}
```

---

What happened?

```css
display:grid;
```

Creates grid layout.

---

```css
repeat(auto-fit,minmax(220px,1fr))
```

Responsive cards.

---

# STEP 5 — First JavaScript

Create:

```javascript
script.js
```

Add:

```javascript
const API_KEY = "YOUR_API_KEY";
```

This stores API key.

---

Add:

```javascript
const searchBtn =
document.getElementById("searchBtn");

const searchInput =
document.getElementById("searchInput");

const moviesContainer =
document.getElementById("moviesContainer");
```

---

Why?

We need references to HTML elements.

---

# STEP 6 — First API Call

Add below previous code:

```javascript
async function searchMovies(movieName){

const response =
await fetch(
`https://www.omdbapi.com/?apikey=${API_KEY}&s=${movieName}`
);

const data =
await response.json();

console.log(data);

}
```

---

Understanding every line:

Function:

```javascript
async function searchMovies()
```

Allows waiting for API response.

---

API call:

```javascript
fetch(...)
```

Sends HTTP request.

---

Wait:

```javascript
await fetch(...)
```

Pauses until response arrives.

---

Convert JSON:

```javascript
await response.json()
```

Converts text into JavaScript object.

---

# STEP 7 — Trigger Search

Add:

```javascript
searchBtn.addEventListener(
"click",
()=>{
const movie =
searchInput.value;

searchMovies(movie);
}
);
```

---

Flow:

```text
Button Click
      ↓
Get Input Value
      ↓
Call searchMovies()
      ↓
Fetch API
      ↓
Console Output
```

---

Now search:

```text
Batman
```

Open:

```text
F12 → Console
```

You will see API data.

---

# STEP 8 — Display Movies

Add function:

```javascript
function displayMovies(movies){

moviesContainer.innerHTML = "";

movies.forEach(movie=>{

moviesContainer.innerHTML +=
`
<div class="movie-card">

<img
src="${movie.Poster}">

<h3>
${movie.Title}
</h3>

<p>
${movie.Year}
</p>

</div>
`;

});

}
```

---

Now modify:

```javascript
searchMovies()
```

Replace:

```javascript
console.log(data);
```

with:

```javascript
displayMovies(data.Search);
```

---

Now API data appears on screen.

---

# STEP 9 — Style Movie Cards

Add CSS:

```css
.movie-card{

background:#222;

padding:10px;

border-radius:10px;

transition:.3s;
}

.movie-card:hover{
transform:scale(1.05);
}

.movie-card img{

width:100%;

height:320px;

object-fit:cover;

border-radius:10px;
}

.movie-card h3{
margin-top:10px;
}
```

---

Now cards look professional.

---

# STEP 10 — Handle Errors

Current problem:

If movie not found:

```json
{
"Response":"False"
}
```

Application crashes.

---

Modify:

```javascript
if(data.Response==="False")
{
moviesContainer.innerHTML=
"<h2>No Movies Found</h2>";

return;
}
```

Add before:

```javascript
displayMovies(...)
```

---

# Architecture So Far

```text
User
 ↓

Search Button

 ↓

searchMovies()

 ↓

fetch()

 ↓

JSON

 ↓

displayMovies()

 ↓

DOM Update

 ↓

Screen
```

---

This is Part 1. We have built the foundation and your first real API-powered app.

In Part 2, we'll add:

1. Loading Spinner
2. Enter Key Search
3. Movie Details Page (`movie.html`)
4. Passing Movie ID
5. Second API Call using IMDb ID
6. Full Movie Information Page
7. Better UI Architecture
8. Reusable Functions
9. Clean Code Structure
10. Pagination System

Those features will make it look like a production-level movie application.
