# FORMS 

```html
<!DOCTYPE html>
<html>
<head>
    <title>Student Registration Form</title>
</head>
<body>

<h1>Student Registration Form</h1>

<form action="" method="post" autocomplete="on">

    <fieldset>

        <legend>Personal Details</legend>

        <label for="fname">Full Name :</label>
        <input
            type="text"
            id="fname"
            name="fname"
            placeholder="Enter Full Name"
            required
            minlength="3"
            maxlength="30"
            autofocus>
        <br><br>

        <label for="email">Email :</label>
        <input
            type="email"
            id="email"
            name="email"
            placeholder="Enter Email"
            required>
        <br><br>

        <label for="password">Password :</label>
        <input
            type="password"
            id="password"
            name="password"
            minlength="8"
            maxlength="20"
            required>
        <br><br>

        <label for="phone">Phone :</label>
        <input
            type="tel"
            id="phone"
            name="phone"
            placeholder="9876543210"
            required>
        <br><br>

        <label for="age">Age :</label>
        <input
            type="number"
            id="age"
            name="age"
            min="18"
            max="60"
            step="1">
        <br><br>

    </fieldset>

    <br>

    <fieldset>

        <legend>Gender</legend>

        <input type="radio" id="male" name="gender">
        <label for="male">Male</label>

        <input type="radio" id="female" name="gender">
        <label for="female">Female</label>

        <input type="radio" id="other" name="gender">
        <label for="other">Other</label>

    </fieldset>

    <br>

    <fieldset>

        <legend>Skills Using Select</legend>

        <label>Skills :</label>

        <select multiple>

            <optgroup label="Frontend">

                <option>HTML</option>
                <option>CSS</option>
                <option>JavaScript</option>

            </optgroup>

            <optgroup label="Backend">

                <option>Java</option>
                <option>Python</option>
                <option>NodeJS</option>

            </optgroup>

            <optgroup label="Database">

                <option>SQL</option>
                <option>MongoDB</option>

            </optgroup>

        </select>

    </fieldset>

    <br>

    <fieldset>

        <legend>Skills Using Datalist</legend>

        <label>Search Skill :</label>

        <input list="skillList">

        <datalist id="skillList">

            <option value="HTML">HTML</option>
            <option value="CSS">CSS</option>
            <option value="JavaScript">JavaScript</option>
            <option value="Java">Java</option>
            <option value="Python">Python</option>
            <option value="NodeJS">NodeJS</option>
            <option value="MongoDB">MongoDB</option>

        </datalist>

    </fieldset>

    <br>

    <fieldset>

        <legend>Course Information</legend>

        <label>Course Start Date :</label>
        <input type="date">
        <br><br>

        <label>Preferred Time :</label>
        <input type="time">
        <br><br>

        <label>Date and Time :</label>
        <input type="datetime-local">
        <br><br>

        <label>Week :</label>
        <input type="week">
        <br><br>

        <label>Month :</label>
        <input type="month">

    </fieldset>

    <br>

    <fieldset>

        <legend>Additional Details</legend>

        <label>Portfolio URL :</label>
        <input type="url">
        <br><br>

        <label>Search Course :</label>
        <input type="search">
        <br><br>

        <label>Favorite Color :</label>
        <input type="color">
        <br><br>

        <label>Upload Resume :</label>
        <input type="file">
        <br><br>

        <label>Confidence Level :</label>
        <input
            type="range"
            min="0"
            max="100">

    </fieldset>

    <br>

    <fieldset>

        <legend>Interests</legend>

        <input type="checkbox" id="coding">
        <label for="coding">Coding</label>

        <input type="checkbox" id="sports">
        <label for="sports">Sports</label>

        <input type="checkbox" id="music">
        <label for="music">Music</label>

    </fieldset>

    <br>

    <fieldset>

        <legend>Address</legend>

        <textarea
            rows="5"
            cols="40"
            placeholder="Enter Address">
        </textarea>

    </fieldset>

    <br>

    <fieldset>

        <legend>Readonly and Disabled Example</legend>

        <label>College Name :</label>
        <input
            type="text"
            value="GITAM University"
            readonly>
        <br><br>

        <label>Scholarship Status :</label>
        <input
            type="text"
            value="Not Available"
            disabled>

    </fieldset>

    <br>

    <button type="submit">
        Submit
    </button>

    <button type="reset">
        Reset
    </button>

</form>

</body>
</html>
```

# Project: My College Website Layout

### Objective

Build a complete webpage using:

* header
* nav
* main
* section
* article
* aside
* footer

---

# Expected Output Structure

```text
--------------------------------------------------
|                    HEADER                        |
|             GITAM UNIVERSITY                     |
--------------------------------------------------
| HOME | ABOUT | COURSES | CONTACT | GALLERY      |
--------------------------------------------------

|                 MAIN CONTENT                     |
|--------------------------------------------------|
|                                                  |
|  SECTION 1                                       |
|  About College                                   |
|                                                  |
|  ARTICLE                                         |
|  Latest Placement News                           |
|                                                  |
|--------------------------------------------------|
|                                                  |
|  SECTION 2                                       |
|  Popular Courses                                 |
|                                                  |
----------------------------------------------------

| ASIDE                                            |
| Upcoming Events                                  |
| Placement Drive                                  |
| Workshop                                         |
----------------------------------------------------

| FOOTER                                           |
| Copyright 2026                                   |
----------------------------------------------------
```

---

# Complete HTML Project

```html
<!DOCTYPE html>
<html>

<head>
    <title>My College Website</title>
</head>

<body>

    <header>
        <h1>GITAM UNIVERSITY</h1>
        <p>Welcome to Our Official Website</p>
    </header>

    <hr>

    <nav>
        <a href="#">Home</a> |
        <a href="#">About</a> |
        <a href="#">Courses</a> |
        <a href="#">Gallery</a> |
        <a href="#">Contact</a>
    </nav>

    <hr>

    <main>

        <section>
            <h2>About College</h2>

            <p>
                GITAM University is one of the leading educational
                institutions providing quality education.
            </p>
        </section>

        <hr>

        <article>

            <h2>Latest Placement News</h2>

            <p>
                500+ students got placed in top MNC companies
                during the latest placement drive.
            </p>

        </article>

        <hr>

        <section>

            <h2>Popular Courses</h2>

            <ul>
                <li>B.Tech</li>
                <li>M.Tech</li>
                <li>MBA</li>
                <li>BCA</li>
            </ul>

        </section>

    </main>

    <hr>

    <aside>

        <h2>Upcoming Events</h2>

        <ul>
            <li>Placement Drive</li>
            <li>Python Workshop</li>
            <li>Hackathon</li>
        </ul>

    </aside>

    <hr>

    <footer>

        <p>
            Copyright © 2026 GITAM University
        </p>

    </footer>

</body>

</html>
```

---

# Real World Project

### Personal Portfolio Layout

This project covers all important semantic tags.

```html
<!DOCTYPE html>
<html>

<head>
    <title>Portfolio</title>
</head>

<body>

    <header>
        <h1>Paindla Narendar Reddy</h1>
        <p>Full Stack Developer | Trainer</p>
    </header>

    <hr>

    <nav>
        <a href="#">About</a>
        <a href="#">Skills</a>
        <a href="#">Projects</a>
        <a href="#">Contact</a>
    </nav>

    <hr>

    <main>

        <section>

            <h2>About Me</h2>

            <p>
                Passionate Full Stack Developer and Trainer.
            </p>

        </section>

        <section>

            <h2>Skills</h2>

            <ul>
                <li>Java</li>
                <li>Spring Boot</li>
                <li>MongoDB</li>
                <li>ReactJS</li>
            </ul>

        </section>

        <article>

            <h2>Project Highlight</h2>

            <p>
                Developed VillageBasket Food Delivery Application.
            </p>

        </article>

    </main>

    <aside>

        <h3>Quick Links</h3>

        <ul>
            <li>GitHub</li>
            <li>LinkedIn</li>
            <li>Resume</li>
        </ul>

    </aside>

    <footer>

        <p>
            Email : narendar@example.com
        </p>

    </footer>

</body>

</html>
```

---

# Best Classroom Activity

### Task: Create a News Website

Requirements:

1. Use `<header>` for website title.
2. Use `<nav>` for categories.
3. Use `<main>` for main content.
4. Use `<section>` for Sports News.
5. Use `<section>` for Technology News.
6. Use `<article>` for individual news articles.
7. Use `<aside>` for advertisements.
8. Use `<footer>` for copyright.

---

# Interview Questions

### What are Semantic Tags?

Semantic tags describe the meaning or purpose of the content.

Example:

```html
<header>
<footer>
<nav>
<section>
<article>
<aside>
<main>
```

---

### Why were Semantic Tags introduced?

Before HTML5:

```html
<div id="header"></div>

<div id="footer"></div>
```

Difficult to understand.

After HTML5:

```html
<header></header>

<footer></footer>
```

Easy to understand.

---

### Difference Between Semantic and Non-Semantic Tags

| Semantic | Non-Semantic |
| -------- | ------------ |
| header   | div          |
| footer   | span         |
| nav      | div          |
| section  | div          |
| article  | div          |

---


For teaching **Audio, Video, and Iframe**, don't stop with only `src`, `controls`, `autoplay`, etc. There are some important tags and attributes that students should know.

# 1. Audio Tag

The `<audio>` tag is used to embed sound files into a webpage.

## Syntax

```html
<audio src="song.mp3"></audio>
```

---

## Important Attributes

| Attribute | Purpose                   |
| --------- | ------------------------- |
| src       | Path of audio file        |
| controls  | Shows play/pause controls |
| autoplay  | Plays automatically       |
| loop      | Repeats audio             |
| muted     | Starts audio muted        |
| preload   | Loads audio before play   |

---

## Simple Example

```html
<audio src="song.mp3" controls>
</audio>
```

---

## Full Example

```html
<audio
src="song.mp3"
controls
autoplay
loop
muted>
</audio>
```

---

## Alternative Method (Recommended)

```html
<audio controls>

<source src="song.mp3" type="audio/mpeg">

Your browser does not support audio.

</audio>
```

### Why use `<source>`?

Multiple audio formats can be provided.

```html
<audio controls>

<source src="song.mp3" type="audio/mpeg">

<source src="song.ogg" type="audio/ogg">

</audio>
```

---

# 2. Video Tag

The `<video>` tag is used to embed videos.

## Syntax

```html
<video src="video.mp4"></video>
```

---

## Important Attributes

| Attribute | Purpose         |
| --------- | --------------- |
| src       | Video path      |
| controls  | Video controls  |
| autoplay  | Auto play       |
| loop      | Repeat video    |
| muted     | Mute video      |
| poster    | Thumbnail image |
| width     | Width           |
| height    | Height          |
| preload   | Preload video   |

---

## Simple Example

```html
<video src="video.mp4" controls>
</video>
```

---

## Complete Example

```html
<video
src="video.mp4"
controls
width="500"
height="300"
poster="thumbnail.jpg">
</video>
```

---

## Recommended Method

```html
<video controls width="500">

<source src="video.mp4" type="video/mp4">

Your browser does not support video.

</video>
```

---

## Multiple Formats

```html
<video controls>

<source src="video.mp4" type="video/mp4">

<source src="video.webm" type="video/webm">

</video>
```

---

# 3. Iframe Tag

The `<iframe>` tag is used to display another webpage inside the current webpage.

---

## Syntax

```html
<iframe src="page.html"></iframe>
```

---

## Important Attributes

| Attribute   | Purpose       |
| ----------- | ------------- |
| src         | Page URL      |
| width       | Width         |
| height      | Height        |
| frameborder | Border        |
| loading     | Lazy loading  |
| title       | Accessibility |

---

## Internal Page Example

### home.html

```html
<h1>Welcome to Home Page</h1>
```

### index.html

```html
<iframe
src="home.html"
width="500"
height="300">
</iframe>
```

---

## Website Example

```html
<iframe
src="https://www.wikipedia.org"
width="800"
height="500">
</iframe>
```

---

## YouTube Example

```html
<iframe
width="560"
height="315"
src="https://www.youtube.com/embed/dQw4w9WgXc">
</iframe>
```

---

## Google Maps Example

```html
<iframe
src="https://www.google.com/maps/embed?...">
</iframe>
```

---

# Hands-On Project

## Project: Multimedia Learning Portal

### Output

```text
--------------------------------------
|     Multimedia Learning Portal     |
--------------------------------------

Audio Section
[ Play Audio ]

Video Section
[ Play Video ]

YouTube Tutorial
[ Embedded YouTube Video ]

Location
[ Google Map ]

--------------------------------------
|            Footer                  |
--------------------------------------
```

---

## Complete HTML Project

```html
<!DOCTYPE html>
<html>

<head>
    <title>Multimedia Learning Portal</title>
</head>

<body>

<header>
    <h1>Multimedia Learning Portal</h1>
</header>

<hr>

<section>

    <h2>Audio Lesson</h2>

    <audio controls>

        <source src="song.mp3" type="audio/mpeg">

        Audio Not Supported

    </audio>

</section>

<hr>

<section>

    <h2>Video Lesson</h2>

    <video
    controls
    width="500"
    poster="thumbnail.jpg">

        <source src="video.mp4" type="video/mp4">

        Video Not Supported

    </video>

</section>

<hr>

<section>

    <h2>YouTube Tutorial</h2>

    <iframe
    width="560"
    height="315"
    src="https://www.youtube.com/embed/jS4aFq5-91M">

    </iframe>

</section>

<hr>

<section>

    <h2>College Location</h2>

    <iframe
    src="https://maps.google.com"
    width="600"
    height="300">

    </iframe>

</section>

<hr>

<footer>

    <p>
        Copyright © 2026 Multimedia Learning Portal
    </p>

</footer>

</body>
</html>
```

---

# Classroom Assignment

Create a **Movie Review Website** using:

### Header

* Website Title

### Section 1

* Movie Trailer using Video Tag

### Section 2

* Theme Song using Audio Tag

### Section 3

* YouTube Interview using Iframe

### Section 4

* Theatre Location using Google Maps Iframe

### Footer

* Copyright Information

---

# Missing Tags Students Should Also Know

When teaching Audio and Video, also introduce:

### Source Tag

```html
<source>
```

Used inside:

```html
<audio>
<video>
```

To provide multiple file formats.

---

### Track Tag

Used for subtitles in videos.

```html
<video controls>

<source src="movie.mp4">

<track
src="subtitles.vtt"
kind="subtitles"
srclang="en"
label="English">

</video>
```

---

# Interview Questions

### Difference Between Audio and Video?

| Audio          | Video          |
| -------------- | -------------- |
| Sound only     | Sound + Visual |
| Uses `<audio>` | Uses `<video>` |

---

### Why use Source Tag?

To support multiple media formats.

---

### What is Poster Attribute?

Thumbnail image shown before video starts.

---

### What is Iframe?

Used to embed another webpage inside the current webpage.

---

### Can YouTube videos be embedded using iframe?

**Yes**

```html
<iframe src="youtube-embed-link"></iframe>
```


