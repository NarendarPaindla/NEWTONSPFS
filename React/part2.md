# Module 1 – Introduction to React

# Topic 2: Why React Exists – Problems with Traditional DOM Manipulation

Today, almost every software company uses React, but React wasn't created because JavaScript was bad. It was created because **building large applications with plain JavaScript became difficult to maintain.**

Let's understand this by building a small application.

---

# Step 1: Build a Counter Using HTML + JavaScript

Create a folder named **dom-counter**.

Inside it, create three files.

```
dom-counter/
│
├── index.html
├── style.css
└── script.js
```

---

## index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DOM Counter</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

    <div class="container">
        <h1>Counter Application</h1>

        <h2 id="count">0</h2>

        <button id="incrementBtn">Increment</button>

        <button id="decrementBtn">Decrement</button>

        <button id="resetBtn">Reset</button>
    </div>

<script src="script.js"></script>

</body>
</html>
```

---

## style.css

```css
body{
    font-family: Arial;
    display:flex;
    justify-content:center;
    align-items:center;
    height:100vh;
    background:#f2f2f2;
}

.container{
    background:white;
    padding:30px;
    border-radius:10px;
    text-align:center;
    box-shadow:0px 0px 10px gray;
}

button{
    padding:10px 20px;
    margin:5px;
    cursor:pointer;
}
```

---

## script.js

```javascript
let count = 0;

const countElement = document.getElementById("count");

const incrementBtn = document.getElementById("incrementBtn");

const decrementBtn = document.getElementById("decrementBtn");

const resetBtn = document.getElementById("resetBtn");

incrementBtn.addEventListener("click", () => {

    count++;

    countElement.innerText = count;

});

decrementBtn.addEventListener("click", () => {

    count--;

    countElement.innerText = count;

});

resetBtn.addEventListener("click", () => {

    count = 0;

    countElement.innerText = count;

});
```

Run it by opening **index.html** in your browser.

Everything works perfectly.

So why do we need React?

---

# Step 2: What Is Happening Internally?

When you click **Increment**, JavaScript performs these steps.

```
Button Click
      │
      ▼
Increase Variable
      │
      ▼
Find HTML Element
      │
      ▼
Update DOM
```

Notice this line.

```javascript
countElement.innerText = count;
```

We manually update the DOM.

JavaScript does **not** know what changed.

We have to tell it.

---

# Understanding the DOM

DOM stands for **Document Object Model**.

When the browser loads an HTML page, it converts it into a tree structure.

Example HTML

```html
<body>

    <h1>Hello</h1>

    <button>Click</button>

</body>
```

DOM Tree

```
Document
    │
   html
     │
    body
   ├───────┐
  h1     button
```

JavaScript manipulates this tree.

Example

```javascript
document.querySelector("h1").innerText = "Welcome";
```

You are directly changing a node in the DOM tree.

---

# DOM Manipulation

DOM manipulation means changing HTML elements using JavaScript.

Examples

Changing text

```javascript
element.innerText = "Hello";
```

Changing color

```javascript
element.style.color = "red";
```

Adding HTML

```javascript
element.innerHTML = "<h1>React</h1>";
```

Removing elements

```javascript
element.remove();
```

Creating elements

```javascript
const p = document.createElement("p");
```

Appending elements

```javascript
document.body.appendChild(p);
```

These operations are called **DOM Manipulation**.

---

# Small Applications Are Easy

Our counter application contains only

* 1 heading
* 3 buttons
* 1 variable

Managing it is simple.

---

# Imagine Building Instagram

Now imagine a page containing:

```
Navbar

Stories

Posts

Comments

Likes

Notifications

Messages

Sidebar

Advertisements

Profile Card

Footer
```

Hundreds or even thousands of HTML elements.

When a user likes one post:

* Update the like count
* Change the heart icon
* Update notifications
* Refresh profile activity
* Sync with server
* Update another page

With plain JavaScript, developers manually write all these updates.

The application becomes difficult to maintain.

---

# Real Example

Suppose your webpage has

```
1000 HTML Elements
```

User changes one name.

Without an efficient approach, developers often need to:

* Find the correct element
* Update text
* Update parent
* Update child
* Update styles
* Keep everything synchronized

This becomes error-prone.

---

# Problems with Traditional DOM Manipulation

## 1. Too Much Manual Work

Example

```javascript
document.getElementById("name").innerText = username;
```

You have to find every element manually.

---

## 2. Code Duplication

Suppose five places display the user's name.

```
Navbar

Sidebar

Profile

Dashboard

Settings
```

You'll update all five separately.

```javascript
navbar.innerText = username;

profile.innerText = username;

sidebar.innerText = username;

settings.innerText = username;
```

Easy to forget one.

---

## 3. Difficult Maintenance

As projects grow,

JavaScript files become:

```
3000 Lines

5000 Lines

10000 Lines
```

Finding bugs becomes harder.

---

## 4. Performance Issues

The browser spends time updating the DOM.

DOM operations are relatively expensive compared to updating JavaScript variables.

Frequent unnecessary DOM updates can make applications slower.

---

## 5. No Component Reuse

Suppose you create this button.

```
Login Button
```

You want it in 20 places.

Without components, you'll often duplicate HTML or manually generate it.

If you later change the button style, every copy must be updated.

---

# React's Idea

Instead of developers manually updating the DOM,

React says:

> "Just describe what the UI should look like based on the current data. I'll figure out the most efficient way to update the real DOM."

Traditional JavaScript

```
Developer
      │
Updates DOM
      │
Browser
```

React

```
Developer
      │
Updates State
      │
React
      │
Calculates Changes
      │
Updates DOM Efficiently
```

This automation is one of React's biggest strengths.

---

# Real-Life Analogy

Imagine a library with 10,000 books.

A student requests one book.

### Traditional Approach

You rebuild the entire library.

Very inefficient.

### React Approach

You replace only the requested book.

Simple and fast.

---

# Hands-on Activity

Extend the counter application by adding:

1. A **"Multiply by 2"** button.
2. A **"Decrease by 5"** button.
3. Display the message:

   * `"Positive"` if count > 0
   * `"Negative"` if count < 0
   * `"Zero"` if count === 0

Try implementing it using plain JavaScript. As you add features, notice how the amount of DOM manipulation grows. This will make React's approach much easier to appreciate in upcoming lessons.

---

# Interview Questions

### 1. What is the DOM?

**Answer:**
The DOM (Document Object Model) is a tree-like representation of an HTML document that allows JavaScript to access and modify page elements dynamically.

---

### 2. What is DOM manipulation?

**Answer:**
DOM manipulation is the process of using JavaScript to create, update, remove, or modify HTML elements and their properties.

---

### 3. Why is direct DOM manipulation difficult in large applications?

**Answer:**
Because developers must manually locate and update every affected element, leading to repetitive code, maintenance challenges, higher chances of bugs, and reduced performance as applications grow.

---

### 4. What problem does React solve?

**Answer:**
React simplifies UI development by allowing developers to describe the desired UI while React efficiently determines and performs the minimal DOM updates required.

---

# Key Takeaways

* The **DOM** is the browser's representation of an HTML page.
* Traditional JavaScript requires **manual DOM manipulation**.
* As applications become larger, manual updates become harder to maintain.
* React was created to automate UI updates and improve maintainability and performance.
* In React, developers focus on **what the UI should look like**, while React handles **how to update the DOM efficiently**.

---

### Next Topic

**Topic 3: Virtual DOM – What It Is, How It Works, and Why It Makes React Fast**, where we'll learn the core concept that enables React's efficient updates.
