# Module 1 – Introduction to React

# Topic 7: React Components – The Building Blocks of React Applications

> **If JSX is the language of React, then Components are the heart of React.**
>
> Every React application, whether it's YouTube, Netflix, Amazon, or Instagram, is built using components.

Today, we will learn everything about components from scratch.

---

# Learning Objectives

By the end of this lesson, you will understand:

* What is a Component?
* Why Components exist
* Types of Components
* Creating your first Component
* Component Naming Rules
* Exporting & Importing Components
* Reusing Components
* How React renders Components
* Best Practices
* Hands-on Project

---

# Step 1: What is a Component?

A **Component** is a reusable piece of UI that encapsulates its own structure, behavior, and styling.

Think of it like a LEGO block.

One LEGO block can be reused to build many different structures.

Similarly, one React component can be reused multiple times across an application.

---

# Real World Example

Think about YouTube.

A YouTube homepage contains:

```text
-------------------------------------
Logo     Search     Profile
-------------------------------------

Video Card
Video Card
Video Card
Video Card

-------------------------------------
Footer
-------------------------------------
```

Is every video manually written?

No.

There is one **VideoCard Component**.

React simply reuses it with different data.

---

# Another Example

Amazon

```text
Header

Product Card
Product Card
Product Card
Product Card

Footer
```

Instead of writing the HTML for every product again and again, Amazon creates one `ProductCard` component and reuses it.

---

# Without Components

Imagine writing this repeatedly.

```html
<div class="card">
    <h2>iPhone</h2>
    <p>₹70000</p>
</div>

<div class="card">
    <h2>Samsung</h2>
    <p>₹50000</p>
</div>

<div class="card">
    <h2>OnePlus</h2>
    <p>₹45000</p>
</div>

<div class="card">
    <h2>Pixel</h2>
    <p>₹65000</p>
</div>
```

Hundreds of lines.

Difficult to maintain.

---

# With Components

```jsx
<ProductCard />

<ProductCard />

<ProductCard />

<ProductCard />
```

Much cleaner.

---

# React Applications Are Component Trees

Every React application is actually a tree of components.

Example:

```text
App
│
├── Navbar
│
├── Sidebar
│
├── Dashboard
│      │
│      ├── Card
│      ├── Card
│      ├── Card
│
├── Footer
```

The `App` component is usually the root component.

---

# Types of Components

Historically, React had:

* Class Components
* Function Components

Today, **Function Components** are the standard.

Class Components still exist but are rarely used in new projects.

---

# Function Component

A React Function Component is simply a JavaScript function that returns JSX.

Example:

```jsx
function Welcome() {
    return (
        <h1>Welcome to React</h1>
    );
}
```

That's it.

A component is just a function.

---

# How React Uses It

When React sees:

```jsx
<Welcome />
```

It internally calls:

```javascript
Welcome();
```

The returned JSX is rendered to the screen.

---

# Creating Your First Component

Open

```text
src/App.jsx
```

Replace it with:

```jsx
function App() {

    return (

        <h1>Hello React</h1>

    );

}

export default App;
```

Run it.

Output

```text
Hello React
```

Congratulations!

You have already written your first component.

---

# Creating Another Component

Now create a new file.

```text
src/Header.jsx
```

Write:

```jsx
function Header() {

    return (

        <h1>My Website</h1>

    );

}

export default Header;
```

---

# Import the Component

Open

```text
App.jsx
```

```jsx
import Header from "./Header";

function App() {

    return (

        <Header />

    );

}

export default App;
```

Output

```text
My Website
```

React imported another component.

---

# How Import Works

```text
App

↓

Imports Header

↓

Renders Header

↓

Browser Displays Header
```

---

# Multiple Components

Create another file.

```text
src/Footer.jsx
```

```jsx
function Footer() {

    return (

        <h2>Copyright 2026</h2>

    );

}

export default Footer;
```

Now

```jsx
import Header from "./Header";
import Footer from "./Footer";

function App() {

    return (

        <>
            <Header />

            <h1>Home Page</h1>

            <Footer />
        </>

    );

}

export default App;
```

Output

```text
My Website

Home Page

Copyright 2026
```

Now your application consists of three components.

---

# Component Naming Rules

## Rule 1

Component names always start with a capital letter.

Correct

```jsx
Header
```

Wrong

```jsx
header
```

Why?

Because lowercase names are treated as HTML elements.

Example

```jsx
<header>
```

This is an HTML tag.

Whereas:

```jsx
<Header />
```

This is a React component.

---

# Rule 2

One Component Per File

Good

```text
Header.jsx

Footer.jsx

Navbar.jsx
```

Avoid placing many unrelated components in one file as projects grow.

---

# Rule 3

Component File Names

Usually:

```text
Navbar.jsx

Footer.jsx

Dashboard.jsx

LoginForm.jsx
```

Keep file names meaningful and match the component name.

---

# Component Reusability

Suppose

```jsx
function Button() {

    return (

        <button>Login</button>

    );

}

export default Button;
```

Now use it.

```jsx
<Button />

<Button />

<Button />
```

Output

```text
Login

Login

Login
```

Same component.

Multiple usages.

This is the biggest advantage of React.

---

# How React Renders Components

Suppose

```jsx
<App />
```

contains

```jsx
<Header />

<Footer />
```

React does:

```text
Render App

↓

Render Header

↓

Render Footer

↓

Combine Everything

↓

Display UI
```

---

# Best Practices

✅ Keep components small.

Good

```text
Header

Footer

Sidebar

Card

Button
```

Avoid creating one huge component containing everything.

Large components become difficult to understand and maintain.

---

# Real Company Folder Structure

As projects grow, you'll organize components like this:

```text
src/
│
├── components/
│     ├── Header.jsx
│     ├── Footer.jsx
│     ├── Navbar.jsx
│     ├── Sidebar.jsx
│     └── Button.jsx
│
├── pages/
│
├── assets/
│
├── App.jsx
│
└── main.jsx
```

We'll gradually move to this structure as our application becomes larger.

---

# Hands-on Project

Let's build a simple website using components.

## Step 1

Create:

```text
src/components/
```

---

## Step 2

Create:

```text
Header.jsx
```

```jsx
function Header() {
    return (
        <header>
            <h1>React Learning Portal</h1>
        </header>
    );
}

export default Header;
```

---

## Step 3

Create:

```text
MainContent.jsx
```

```jsx
function MainContent() {
    return (
        <main>
            <h2>Welcome to React Components</h2>

            <p>
                Components make applications reusable and easier to maintain.
            </p>
        </main>
    );
}

export default MainContent;
```

---

## Step 4

Create:

```text
Footer.jsx
```

```jsx
function Footer() {
    return (
        <footer>
            <h3>© 2026 React Course</h3>
        </footer>
    );
}

export default Footer;
```

---

## Step 5

Update `App.jsx`

```jsx
import Header from "./components/Header";
import MainContent from "./components/MainContent";
import Footer from "./components/Footer";

function App() {
    return (
        <>
            <Header />

            <MainContent />

            <Footer />
        </>
    );
}

export default App;
```

---

## Output

```text
----------------------------------

React Learning Portal

Welcome to React Components

Components make applications reusable
and easier to maintain.

© 2026 React Course

----------------------------------
```

You have now created your first multi-component React application.

---

# Common Beginner Mistakes

❌ Component name starts with lowercase.

```jsx
function header() {}
```

---

❌ Forgetting `export default`.

```jsx
function Header() {}
```

Then trying to import it elsewhere will cause an error.

---

❌ Forgetting to import the component.

```jsx
<Header />
```

without

```jsx
import Header from "./Header";
```

---

❌ Returning multiple sibling elements without a wrapper.

```jsx
return (
    <h1>Hello</h1>
    <p>React</p>
);
```

Use a Fragment:

```jsx
return (
    <>
        <h1>Hello</h1>
        <p>React</p>
    </>
);
```

---

# Interview Questions

### 1. What is a React Component?

**Answer:**
A React component is a reusable piece of UI implemented as a JavaScript function (or historically a class) that returns JSX.

---

### 2. Why do we use components?

**Answer:**
Components improve code reusability, readability, maintainability, and make it easier to build and organize complex user interfaces.

---

### 3. What are the types of React components?

**Answer:**
Historically, React supported Class Components and Function Components. Modern React development primarily uses Function Components.

---

### 4. Why should component names start with a capital letter?

**Answer:**
React treats lowercase tags as HTML elements and uppercase names as custom React components.

---

### 5. Can one component be reused multiple times?

**Answer:**
Yes. One of the main advantages of React is that components are reusable and can be rendered multiple times in different parts of an application.

---

# Key Takeaways

* A **component** is a reusable piece of UI.
* Modern React applications are built using **Function Components**.
* Components return **JSX**.
* Component names must start with a **capital letter**.
* Use `import` and `export` to organize components across files.
* Large applications are structured as a **tree of components**.

---

## Next Topic

**Topic 8: Props (Properties)** — you'll learn how to pass data from one component to another, making components dynamic and reusable. We'll build reusable `ProductCard`, `StudentCard`, and `EmployeeCard` components using props, just like in real-world React applications.
