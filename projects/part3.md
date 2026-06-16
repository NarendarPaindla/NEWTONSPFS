Excellent. Now we enter the part that separates a beginner project from a professional frontend project.

# Part 3 — Error Handling + Pagination + LocalStorage Favorites

Current app:

```text
Search
 ↓
Movies
 ↓
Click
 ↓
Details
```

Professional app:

```text
Search
 ↓
Loading
 ↓
Error Handling
 ↓
Pagination
 ↓
Movie Details
 ↓
Save Favorites
 ↓
Persistent Storage
```

---

# STEP 25 — Professional Error Handling

Current code:

```javascript
async function searchMovies(movieName){

const response =
await fetch(
`https://www.omdbapi.com/?apikey=${API_KEY}&s=${movieName}`
);

const data =
await response.json();

displayMovies(data.Search);

}
```

Problem:

If:

* Internet disconnects
* API fails
* Invalid JSON

App crashes.

---

## Replace Entire searchMovies()

Replace with:

```javascript
async function searchMovies(movieName){

try{

loader.style.display="block";

moviesContainer.innerHTML="";

const response =
await fetch(
`https://www.omdbapi.com/?apikey=${API_KEY}&s=${movieName}&page=${currentPage}`
);

const data =
await response.json();

loader.style.display="none";

if(data.Response==="False"){

moviesContainer.innerHTML=
"<h2>No Movies Found</h2>";

return;
}

displayMovies(data.Search);

}
catch(error){

loader.style.display="none";

moviesContainer.innerHTML=

`
<h2>
Something went wrong
</h2>
`;

console.log(error);

}

}
```

---

# Understanding try-catch

```javascript
try{
}
```

Attempt code.

---

```javascript
catch(error){
}
```

If error happens.

---

Example:

```javascript
try{

console.log(user.name);

}
catch(error){

console.log(error);

}
```

No crash.

---

# STEP 26 — OMDb Pagination

Many beginners don't know:

OMDb returns only:

```text
10 movies per page
```

Example:

Batman search:

```text
Page 1 → Movies 1-10
Page 2 → Movies 11-20
Page 3 → Movies 21-30
```

---

# STEP 27 — Create Pagination Variables

At top of:

```javascript
script.js
```

add:

```javascript
let currentPage = 1;

let currentSearch = "";
```

---

# Why?

We need to remember:

```text
Current Search
Current Page
```

---

# STEP 28 — Save Current Search

Find:

```javascript
searchBtn.addEventListener
```

Replace with:

```javascript
searchBtn.addEventListener(
"click",
()=>{

currentSearch =
searchInput.value;

currentPage = 1;

searchMovies(currentSearch);

}
);
```

---

Now app remembers:

```text
Batman
```

---

# STEP 29 — Create Pagination UI

Open:

```html
index.html
```

Below:

```html
<div id="moviesContainer">
</div>
```

Add:

```html
<div class="pagination">

<button id="prevBtn">
Previous
</button>

<span id="pageNumber">
1
</span>

<button id="nextBtn">
Next
</button>

</div>
```

---

# STEP 30 — Style Pagination

Add CSS:

```css
.pagination{

display:flex;

justify-content:center;

align-items:center;

gap:20px;

padding:20px;
}

.pagination button{

padding:10px 20px;
}
```

---

# STEP 31 — Get Pagination Elements

Add near top:

```javascript
const prevBtn =
document.getElementById("prevBtn");

const nextBtn =
document.getElementById("nextBtn");

const pageNumber =
document.getElementById("pageNumber");
```

---

# STEP 32 — Next Page Button

Add:

```javascript
nextBtn.addEventListener(
"click",
()=>{

currentPage++;

pageNumber.textContent =
currentPage;

searchMovies(
currentSearch
);

}
);
```

---

Flow:

```text
Batman
 ↓

Page 1

 ↓

Next

 ↓

Page 2

 ↓

New API Request
```

---

# STEP 33 — Previous Page

Add:

```javascript
prevBtn.addEventListener(
"click",
()=>{

if(currentPage===1)
return;

currentPage--;

pageNumber.textContent =
currentPage;

searchMovies(
currentSearch
);

}
);
```

---

Now:

```text
Page 3
 ↓
Previous
 ↓
Page 2
```

---

# STEP 34 — LocalStorage Introduction

Imagine:

User saves:

```text
Batman Begins
```

Refresh page.

Movie should still be saved.

Need storage.

Browser provides:

```javascript
localStorage
```

---

Think of it as:

```text
Small Database
inside browser
```

---

# STEP 35 — Create Favorite Button

Open:

```javascript
movie.js
```

Find:

```javascript
displayMovie()
```

Inside template string add:

```html
<button
onclick="saveFavorite()">

Add To Favorites

</button>
```

Place after:

```html
<h1>${movie.Title}</h1>
```

Example:

```html
<h1>${movie.Title}</h1>

<button
onclick="saveFavorite()">

Add To Favorites

</button>
```

---

# STEP 36 — Store Current Movie

At top of:

```javascript
movie.js
```

add:

```javascript
let currentMovie = null;
```

---

Find:

```javascript
displayMovie(movie)
```

Add first line:

```javascript
currentMovie = movie;
```

Result:

```javascript
function displayMovie(movie){

currentMovie = movie;

...
}
```

---

Why?

Need access later.

---

# STEP 37 — Save Favorite Function

Add below:

```javascript
function saveFavorite(){

let favorites =

JSON.parse(
localStorage.getItem("favorites")
)
||
[];

favorites.push(currentMovie);

localStorage.setItem(

"favorites",

JSON.stringify(favorites)

);

alert(
"Added to Favorites"
);

}
```

---

# Understanding Every Line

Read storage:

```javascript
localStorage.getItem()
```

---

Convert string → array:

```javascript
JSON.parse()
```

---

Add movie:

```javascript
favorites.push()
```

---

Convert array → string:

```javascript
JSON.stringify()
```

---

Save:

```javascript
localStorage.setItem()
```

---

# Example Storage

Before:

```javascript
[]
```

---

After:

```javascript
[
{
Title:"Batman Begins"
}
]
```

---

# STEP 38 — Create Favorites Page

Create:

```text
favorites.html
```

---

Paste:

```html
<!DOCTYPE html>
<html>

<head>

<title>
Favorites
</title>

<link
rel="stylesheet"
href="style.css">

</head>

<body>

<h1>
Favorite Movies
</h1>

<div id="favoritesContainer">

</div>

<script src="favorites.js"></script>

</body>

</html>
```

---

# STEP 39 — Create favorites.js

Create:

```javascript
favorites.js
```

---

Paste:

```javascript
const favoritesContainer =

document.getElementById(
"favoritesContainer"
);

const favorites =

JSON.parse(
localStorage.getItem(
"favorites"
)
)
||
[];
```

---

# STEP 40 — Display Favorites

Add:

```javascript
favorites.forEach(movie=>{

favoritesContainer.innerHTML +=

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
```

---

Now:

```text
Save Favorite
 ↓

Stored in Browser
 ↓

Open favorites.html
 ↓

Movie Appears
```

---

# STEP 41 — Add Favorites Navigation

Open:

```html
index.html
```

Inside header:

```html
<a href="favorites.html">

Favorites

</a>
```

Example:

```html
<header>

<h1>MovieVerse</h1>

<a href="favorites.html">

Favorites

</a>

...
```

---

# Current Architecture

```text
Movie Search
      ↓

OMDb API

      ↓

Movie Cards

      ↓

Movie Details

      ↓

Add Favorite

      ↓

LocalStorage

      ↓

favorites.html

      ↓

Display Saved Movies
```

---

# What You've Learned So Far

### API

```javascript
fetch()
```

---

### Async Programming

```javascript
async
await
```

---

### Error Handling

```javascript
try
catch
```

---

### URL Parameters

```javascript
URLSearchParams
```

---

### Dynamic HTML

```javascript
innerHTML
```

---

### Pagination

```javascript
page=1
page=2
page=3
```

---

### Browser Storage

```javascript
localStorage
```

---

### JSON

```javascript
JSON.parse()

JSON.stringify()
```

---

In Part 4 we'll refactor this into a **professional folder structure** and add:

* Search Debouncing (used in Netflix/Amazon)
* Skeleton Loading UI
* Remove Favorite Button
* Duplicate Favorite Prevention
* Reusable API Service Layer
* Modular JavaScript Files
* Infinite Scroll
* Dark/Light Theme
* Environment Configuration
* MVC Architecture for Frontend

This is where you start thinking like a frontend engineer rather than someone who only knows HTML/CSS/JS syntax.
