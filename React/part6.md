# Module 1 – Introduction to React

# Topic 6: JSX – What It Is, Why React Uses It, and How It Works Internally

> **JSX is one of the biggest reasons React is easy to use.**
>
> Most beginners think JSX is HTML. **It is not HTML.**
>
> Understanding JSX deeply will help you avoid many common mistakes and answer interview questions confidently.

---

# Learning Objectives

By the end of this lesson, you will understand:

* What JSX is
* Why React uses JSX
* How JSX works internally
* JSX vs HTML
* JSX syntax rules
* Embedding JavaScript in JSX
* Expressions in JSX
* Common mistakes
* Hands-on activity

---

# Step 1: What is JSX?

**JSX** stands for **JavaScript XML**.

It is a **syntax extension for JavaScript** that allows us to write UI code in a way that looks similar to HTML.

Example:

```jsx
function App() {
  return (
    <h1>Hello React</h1>
  );
}
```

At first glance, it looks like HTML.

But it is **not HTML**.

It is JSX.

---

# Why Was JSX Created?

Imagine building a UI using only JavaScript.

Without JSX:

```javascript
const heading = document.createElement("h1");

heading.innerText = "Hello React";

document.body.appendChild(heading);
```

Now imagine creating a page like Amazon.

* Header
* Sidebar
* Product Cards
* Footer
* Search Bar
* Cart

Thousands of lines of JavaScript.

It becomes difficult to read.

---

# React Before JSX

Before JSX, React components looked like this:

```javascript
function App() {
    return React.createElement(
        "h1",
        null,
        "Hello React"
    );
}
```

This works.

But imagine writing an entire Netflix homepage this way.

Very difficult.

---

# React with JSX

Now compare.

```jsx
function App() {
    return (
        <h1>Hello React</h1>
    );
}
```

Much cleaner.

Much easier to understand.

---

# Does the Browser Understand JSX?

No.

Browsers understand only:

* HTML
* CSS
* JavaScript

They do **not** understand JSX.

Example:

```jsx
<h1>Hello</h1>
```

If you give this directly to the browser, it will fail.

So who converts it?

Answer:

**Vite + Babel (or an equivalent JSX compiler in the build pipeline).**

---

# What Happens Internally?

You write:

```jsx
function App() {
    return (
        <h1>Hello React</h1>
    );
}
```

During development/build, it is transformed into JavaScript.

Conceptually:

```javascript
function App() {
    return React.createElement(
        "h1",
        null,
        "Hello React"
    );
}
```

Modern React uses a newer JSX transform, but the important idea is the same:

> **JSX is compiled into JavaScript before it reaches the browser.**

Flow:

```text
Developer Writes JSX
        │
        ▼
Vite/JSX Compiler
        │
        ▼
JavaScript
        │
        ▼
Browser Executes It
```

---

# JSX Is an Expression

JSX can be assigned to variables.

Example:

```jsx
const title = <h1>Welcome</h1>;
```

You can return it.

```jsx
function App() {

    const title = <h1>Hello</h1>;

    return title;

}
```

---

# JSX Looks Like HTML But Isn't

Example:

```jsx
<h1>Hello</h1>
```

Looks like HTML.

Actually becomes JavaScript.

Think of JSX as:

> **A nicer way to write React element creation code.**

---

# JSX Rules

These are very important.

---

# Rule 1: Return Only One Parent Element

❌ Wrong

```jsx
function App() {

    return (

        <h1>Hello</h1>

        <p>React</p>

    );

}
```

Error.

React expects a single root element.

---

## Correct

```jsx
function App() {

    return (

        <div>

            <h1>Hello</h1>

            <p>React</p>

        </div>

    );

}
```

---

# Or Use React Fragment

Instead of adding an unnecessary `<div>`:

```jsx
function App() {

    return (

        <>

            <h1>Hello</h1>

            <p>React</p>

        </>

    );

}
```

A Fragment groups elements without adding an extra DOM node.

---

# Rule 2: Always Close Tags

HTML sometimes allows:

```html
<input>
```

JSX does not.

Correct:

```jsx
<input />
```

Image:

```jsx
<img src="logo.png" />
```

Line break:

```jsx
<br />
```

---

# Rule 3: Use `className` Instead of `class`

HTML:

```html
<div class="box"></div>
```

JSX:

```jsx
<div className="box"></div>
```

Why?

Because `class` is a reserved keyword in JavaScript.

---

# Rule 4: Use `htmlFor` Instead of `for`

HTML:

```html
<label for="email">
```

JSX:

```jsx
<label htmlFor="email">
```

---

# Rule 5: JavaScript Goes Inside Curly Braces

Create a variable.

```jsx
const name = "Narendar";
```

Display it.

```jsx
<h1>Hello {name}</h1>
```

Output:

```text
Hello Narendar
```

---

# Expressions in JSX

Numbers

```jsx
<h1>{100}</h1>
```

Output:

```text
100
```

---

Addition

```jsx
<h1>{10 + 20}</h1>
```

Output:

```text
30
```

---

Multiplication

```jsx
<h1>{5 * 6}</h1>
```

Output:

```text
30
```

---

Variables

```jsx
const city = "Hyderabad";

<h2>{city}</h2>
```

---

Boolean Expressions

```jsx
const isStudent = true;

<h2>{isStudent.toString()}</h2>
```

Output:

```text
true
```

> Note: React does **not** render `true`, `false`, `null`, or `undefined` directly unless you convert them to strings or use them in conditional rendering.

---

# Calling Functions

```jsx
function greet() {

    return "Welcome";

}
```

Use it.

```jsx
<h1>{greet()}</h1>
```

Output:

```text
Welcome
```

---

# Object Access

```jsx
const student = {

    name: "Narendar",

    age: 24

};
```

Display.

```jsx
<h1>{student.name}</h1>

<p>{student.age}</p>
```

---

# What Cannot Be Written Inside JSX?

This is wrong:

```jsx
<h1>

if(true){

}

</h1>
```

Why?

Because JSX accepts **expressions**, not JavaScript statements.

`if`, `for`, and `while` are statements.

We'll learn conditional rendering later using appropriate React patterns.

---

# Real-Life Analogy

Imagine you're writing a book.

HTML is like writing in plain text.

JavaScript is like writing instructions.

JSX lets you write the UI naturally while still allowing JavaScript expressions where needed.

It combines readability with programming power.

---

# Hands-on Activity

Replace your `App.jsx` with the following:

```jsx
function App() {

  const name = "Narendar";
  const city = "Hyderabad";
  const age = 24;

  return (
    <>
      <h1>Welcome to React</h1>

      <h2>Name: {name}</h2>

      <h2>City: {city}</h2>

      <h2>Age: {age}</h2>

      <h2>Next Age: {age + 1}</h2>

      <h2>Multiplication: {5 * 10}</h2>
    </>
  );

}

export default App;
```

### Experiment

Try the following:

1. Change `name` to your own name.
2. Change `city` to your city.
3. Add a new variable:

```jsx
const college = "CMR Technical Campus";
```

Display it:

```jsx
<h2>College: {college}</h2>
```

4. Create a function:

```jsx
function greet() {
    return "Good Morning";
}
```

Display it:

```jsx
<h2>{greet()}</h2>
```

Observe how changing variables automatically changes the rendered UI.

---

# Interview Questions

### 1. What is JSX?

**Answer:**
JSX (JavaScript XML) is a syntax extension for JavaScript that allows developers to write React UI using an HTML-like syntax. It is compiled into JavaScript before execution.

---

### 2. Is JSX HTML?

**Answer:**
No. JSX resembles HTML, but it is JavaScript syntax that is transformed into React element creation code during compilation.

---

### 3. Why do we use JSX?

**Answer:**
JSX makes UI code more readable, maintainable, and easier to write compared to manually creating React elements with JavaScript function calls.

---

### 4. Can a browser understand JSX directly?

**Answer:**
No. JSX must be transformed into JavaScript by the build tool before the browser can execute it.

---

### 5. Why do we use `className` instead of `class`?

**Answer:**
Because `class` is a reserved keyword in JavaScript. React uses `className` to specify CSS classes.

---

# Key Takeaways

* JSX stands for **JavaScript XML**.
* JSX is **not HTML**; it is compiled into JavaScript.
* Browsers cannot execute JSX directly.
* React uses JSX to make UI development more readable and maintainable.
* JSX requires:

  * A single parent element.
  * Self-closing tags for empty elements.
  * `className` instead of `class`.
  * `htmlFor` instead of `for`.
  * Curly braces `{}` for embedding JavaScript expressions.

---

## Next Topic

**Topic 7: Your First React Component** — you'll learn what a component is, why components are the foundation of React, how to create reusable components, naming conventions, importing/exporting components, and build a small multi-component application from scratch.
