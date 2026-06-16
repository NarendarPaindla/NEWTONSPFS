Great. Now we'll transform the basic app into something closer to a professional frontend project.

# Part 2 — Loading State + Enter Key + Movie Details Page

Current flow:

```text
Search
  ↓
API Call
  ↓
Display Cards
```

New flow:

```text
Search
  ↓
Loading Spinner
  ↓
API Call
  ↓
Movie Cards
  ↓
Click Card
  ↓
Movie Details Page
  ↓
Another API Call
  ↓
Full Movie Information
```

---

# STEP 11 — Add Loading Spinner

## Why?

Currently user clicks Search and sees nothing for a moment.

Professional apps show:

```text
Loading...
```

or spinner.

---

## HTML Changes

Open:

```html
index.html
```

Find:

```html
<main>

<div id="moviesContainer">

</div>

</main>
```

Replace with:

```html
<main>

<div id="loader" class="loader">
Loading...
</div>

<div id="moviesContainer">

</div>

</main>
```

---

# STEP 12 — Style Loader

Add at bottom of:

```css
style.css
```

```css
.loader{
display:none;

text-align:center;

font-size:22px;

padding:30px;
}
```

---

# STEP 13 — Access Loader in JavaScript

At top of:

```javascript
script.js
```

add:

```javascript
const loader =
document.getElementById("loader");
```

---

# STEP 14 — Show Loader Before API Call

Find:

```javascript
async function searchMovies(movieName)
```

Replace entire function with:

```javascript
async function searchMovies(movieName){

loader.style.display = "block";

moviesContainer.innerHTML = "";

const response =
await fetch(
`https://www.omdbapi.com/?apikey=${API_KEY}&s=${movieName}`
);

const data =
await response.json();

loader.style.display = "none";

if(data.Response === "False"){

moviesContainer.innerHTML =
"<h2>No Movies Found</h2>";

return;
}

displayMovies(data.Search);

}
```

---

# What Changed?

Before API:

```javascript
loader.style.display="block";
```

Show loading.

---

After API:

```javascript
loader.style.display="none";
```

Hide loading.

---

User Experience:

```text
Search
 ↓

Loading...

 ↓

Movies Appear
```

---

# STEP 15 — Search with Enter Key

Currently only button works.

Professional websites allow:

```text
Enter Key
```

---

Add below button event:

```javascript
searchInput.addEventListener(
"keypress",
(event)=>{

if(event.key==="Enter"){

searchMovies(
searchInput.value
);

}

}
);
```

---

Now:

```text
Type Batman
Press Enter
```

works.

---

# STEP 16 — Movie Details Page

Currently:

```text
Batman Begins
```

appears.

But clicking does nothing.

We'll create:

```text
movie.html
```

for full details.

---

# Create New File

```html
pages/movie.html
```

---

Paste:

```html
<!DOCTYPE html>
<html>

<head>

<meta charset="UTF-8">

<meta
name="viewport"
content="width=device-width,initial-scale=1.0">

<title>Movie Details</title>

<link rel="stylesheet"
href="../style.css">

</head>

<body>

<div id="movieDetails">

</div>

<script src="movie.js"></script>

</body>

</html>
```

---

# Project Structure Now

```text
movieverse

index.html

style.css

script.js

movie.js

pages
  └── movie.html
```

---

# STEP 17 — Understand IMDb IDs

OMDb returns:

```json
{
"Title":"Batman Begins",
"imdbID":"tt0372784"
}
```

This ID is unique.

Like:

```text
Aadhar Number
```

for a movie.

---

We will use:

```text
tt0372784
```

to fetch full details.

---

# STEP 18 — Make Movie Cards Clickable

Find:

```javascript
displayMovies()
```

Replace card HTML with:

```javascript
<div
class="movie-card"
onclick="openMovie('${movie.imdbID}')">
```

Full block:

```javascript
moviesContainer.innerHTML +=
`
<div
class="movie-card"
onclick="openMovie('${movie.imdbID}')">

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
```

---

# STEP 19 — Create openMovie Function

Add below:

```javascript
function openMovie(id){

window.location.href =
`pages/movie.html?id=${id}`;

}
```

---

Example:

Click:

```text
Batman Begins
```

Browser opens:

```text
movie.html?id=tt0372784
```

---

# Understanding URL Parameters

URL:

```text
movie.html?id=tt0372784
```

contains:

```text
id = tt0372784
```

We will extract it.

---

# STEP 20 — Create movie.js

Create:

```javascript
movie.js
```

---

Paste:

```javascript
const API_KEY =
"YOUR_API_KEY";
```

---

# STEP 21 — Get Movie ID from URL

Add:

```javascript
const params =
new URLSearchParams(
window.location.search
);

const movieId =
params.get("id");
```

---

Example:

URL:

```text
movie.html?id=tt0372784
```

returns:

```javascript
movieId
```

=

```text
tt0372784
```

---

# STEP 22 — Fetch Full Movie Details

Add:

```javascript
async function getMovieDetails(){

const response =
await fetch(
`https://www.omdbapi.com/?apikey=${API_KEY}&i=${movieId}`
);

const data =
await response.json();

console.log(data);

}

getMovieDetails();
```

---

Notice:

Search page used:

```javascript
&s=
```

because searching.

---

Details page uses:

```javascript
&i=
```

because fetching by IMDb ID.

---

# STEP 23 — Display Full Details

Replace:

```javascript
console.log(data);
```

with:

```javascript
displayMovie(data);
```

---

Create:

```javascript
function displayMovie(movie){

document.getElementById(
"movieDetails"
).innerHTML =

`
<div class="details-container">

<img
src="${movie.Poster}">

<div>

<h1>${movie.Title}</h1>

<p>
Year:
${movie.Year}
</p>

<p>
Genre:
${movie.Genre}
</p>

<p>
Director:
${movie.Director}
</p>

<p>
IMDb Rating:
${movie.imdbRating}
</p>

<p>
${movie.Plot}
</p>

</div>

</div>
`;

}
```

---

# STEP 24 — Style Details Page

Add CSS:

```css
.details-container{

display:flex;

gap:30px;

padding:40px;

align-items:flex-start;
}

.details-container img{

width:300px;

border-radius:10px;
}

.details-container h1{

margin-bottom:15px;
}

.details-container p{

margin-bottom:10px;

line-height:1.6;
}
```

---

# What We Have Now

Search Page:

```text
Batman
 ↓

Movie Cards
```

Click Card:

```text
Batman Begins
```

↓

URL:

```text
movie.html?id=tt0372784
```

↓

Second API Call:

```javascript
https://www.omdbapi.com/
?apikey=KEY
&i=tt0372784
```

↓

Full Movie Details

---

# Important Frontend Concept Learned

You just used **two different API patterns**:

### Search Endpoint

```javascript
?s=batman
```

Returns:

```javascript
[
 movie1,
 movie2,
 movie3
]
```

Many movies.

---

### Detail Endpoint

```javascript
&i=tt0372784
```

Returns:

```javascript
{
 Title,
 Plot,
 Genre,
 Director
}
```

Single movie.

---

# Architecture After Part 2

```text
INDEX PAGE

Search Movie
      ↓

searchMovies()

      ↓

fetch()

      ↓

Movie Cards

      ↓

Click Card

      ↓

openMovie()

      ↓

movie.html?id=xxxx

--------------------------------

DETAIL PAGE

Read URL Parameter

      ↓

IMDb ID

      ↓

getMovieDetails()

      ↓

fetch()

      ↓

Display Full Movie
```

In Part 3 we'll make this feel like a production application by adding:

1. Try/Catch Error Handling
2. Default Poster for Missing Images
3. Pagination (Next Page)
4. Previous Page
5. Page Numbers
6. Search Debouncing
7. Favorites System using LocalStorage
8. Save Movie Button
9. Remove Favorite Button
10. Favorites Page

Those concepts are used in real frontend interviews and commercial projects.
