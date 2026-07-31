# Module 1 – Introduction to React

# Topic 5: Understanding the React Project Structure

> **Goal:** By the end of this lesson, you should know the purpose of every file that Vite creates. This is a common interview topic, and understanding the project structure makes debugging much easier.

---

# Learning Objectives

By the end of this lesson, you will understand:

* Every file and folder created by Vite
* Which files you edit daily
* Which files you should avoid modifying
* How React starts internally
* The application execution flow
* Hands-on activity

---

# Step 1: Create a React Project

If you haven't already:

```bash
npm create vite@latest
```

Project Name:

```text
my-react-app
```

Framework:

```text
React
```

Variant:

```text
JavaScript
```

Then run:

```bash
cd my-react-app

npm install

npm run dev
```

---

# Step 2: Initial Project Structure

After creating the project, you'll see something like this:

```text
my-react-app/
│
├── node_modules/
├── public/
├── src/
├── .gitignore
├── eslint.config.js
├── index.html
├── package.json
├── package-lock.json
├── vite.config.js
└── README.md
```

Let's understand each one.

---

# 1. node_modules/

```text
node_modules/
```

This folder contains **all the installed packages** your project depends on.

Examples:

```text
react

react-dom

vite

eslint

many other packages...
```

These packages are downloaded when you run:

```bash
npm install
```

---

## Can We Edit node_modules?

❌ No.

Never edit files inside `node_modules`.

Reason:

* It can contain thousands of files.
* Changes will be lost after reinstalling dependencies.
* It is managed automatically by npm.

---

## Real-Life Example

Imagine you're cooking.

You buy ingredients from a supermarket.

The supermarket is **node_modules**.

You don't change the supermarket—you use its products.

---

# 2. public/

```text
public/
```

This folder stores **static files**.

Examples:

```text
logo.png

favicon.ico

robots.txt

images/

videos/
```

Files inside `public` are served directly without being processed by Vite.

Example:

```text
public/

    logo.png
```

Access in the browser:

```text
http://localhost:5173/logo.png
```

---

# When Do We Use public?

Use it for assets that should remain unchanged, such as:

* Favicons
* Static PDFs
* Public images
* Manifest files

---

# 3. src/

This is the **most important folder**.

Almost all your React development happens here.

Example:

```text
src/

App.jsx

main.jsx

App.css

index.css

assets/
```

You'll spend most of your time inside `src`.

---

# 4. App.jsx

Open:

```text
src/App.jsx
```

Example:

```jsx
function App() {

  return (
    <>
      <h1>Hello React</h1>
    </>
  );

}

export default App;
```

This is your **main application component**.

Think of it as the root of your application's UI.

As your application grows, you'll import other components into `App`.

---

# 5. main.jsx

This is one of the most important files.

Example:

```jsx
import React from "react";
import ReactDOM from "react-dom/client";

import App from "./App";

import "./index.css";

ReactDOM.createRoot(
    document.getElementById("root")
).render(
    <App />
);
```

This file is the **entry point** of the React application.

It tells React:

> "Render the `App` component inside the HTML element with the id `root`."

---

# Execution Flow

When you run:

```bash
npm run dev
```

The application starts like this:

```text
Browser

     │

Loads index.html

     │

Loads main.jsx

     │

main.jsx renders App.jsx

     │

App.jsx renders Components

     │

User sees UI
```

This flow is essential to understand.

---

# 6. index.html

Unlike traditional HTML projects, React has only **one main HTML file**.

Example:

```html
<body>

    <div id="root"></div>

    <script type="module" src="/src/main.jsx"></script>

</body>
```

Notice:

```html
<div id="root"></div>
```

Initially, this is empty.

React inserts your entire application into this element.

---

# Visual Representation

Before React loads:

```html
<body>

<div id="root"></div>

</body>
```

After React loads:

```html
<body>

<div id="root">

<h1>Hello React</h1>

</div>

</body>
```

Everything you build is rendered inside the `root` div.

---

# 7. package.json

This file contains project metadata and dependencies.

Example:

```json
{
  "name": "my-react-app",
  "version": "1.0.0"
}
```

It also lists installed packages.

Example:

```json
"dependencies": {

    "react": "...",

    "react-dom": "..."

}
```

And project scripts:

```json
"scripts": {

    "dev": "...",

    "build": "...",

    "preview": "..."

}
```

When you type:

```bash
npm run dev
```

npm looks inside `package.json` to determine which command to execute.

---

# 8. package-lock.json

This file records the **exact versions** of installed packages and their dependencies.

Why?

Suppose:

Today:

```text
React 19.1.0
```

Tomorrow:

```text
React 19.2.0
```

Without a lock file, team members might install different versions.

With `package-lock.json`, everyone gets the same dependency tree, improving consistency.

---

# 9. vite.config.js

This is the Vite configuration file.

Example:

```javascript
import { defineConfig } from "vite";

export default defineConfig({});
```

As projects grow, you can configure:

* Aliases
* Plugins
* Proxy settings
* Build options

For now, you usually won't need to modify it.

---

# 10. .gitignore

This file tells Git which files and folders should **not** be tracked.

Example:

```text
node_modules

dist

.env
```

Why ignore `node_modules`?

Because it can be hundreds of megabytes and can always be regenerated with:

```bash
npm install
```

---

# 11. README.md

This file contains project documentation.

Typically it includes:

* Project description
* Installation steps
* Commands
* Notes for developers

In real companies, every project should have a meaningful README.

---

# Complete Project Flow

```text
Developer

     │

Writes App.jsx

     │

main.jsx imports App.jsx

     │

index.html loads main.jsx

     │

React renders App

     │

Browser displays UI
```

Remember this flow—it explains how a React application starts.

---

# Which Files Will You Use Most?

| File            | Usage                                |
| --------------- | ------------------------------------ |
| `src/App.jsx`   | Main application component           |
| `src/main.jsx`  | Entry point                          |
| `src/index.css` | Global styles                        |
| `src/assets/`   | Images, icons, fonts used in the app |
| `package.json`  | Dependencies and scripts             |

---

# Files You Usually Don't Modify

| File                | Reason                                              |
| ------------------- | --------------------------------------------------- |
| `node_modules/`     | Managed by npm                                      |
| `package-lock.json` | Generated automatically                             |
| `README.md`         | Documentation (update when needed)                  |
| `vite.config.js`    | Modify only when configuration changes are required |

---

# Hands-on Activity

1. Open your React project in VS Code.
2. Expand every folder and identify:

   * `src`
   * `public`
   * `package.json`
   * `main.jsx`
   * `App.jsx`
3. Open `App.jsx` and replace its contents with:

```jsx
function App() {
  return (
    <h1>Welcome to My First React App</h1>
  );
}

export default App;
```

4. Save the file and observe the browser update instantly without a full page refresh (thanks to Hot Module Replacement).

5. Open `index.html` and locate the `<div id="root"></div>`. Understand that React renders your entire application into this element.

---

# Interview Questions

### 1. What is the purpose of the `src` folder?

**Answer:**
The `src` folder contains the application's source code, including React components, styles, assets, and business logic.

---

### 2. What is `main.jsx`?

**Answer:**
`main.jsx` is the entry point of a React application. It renders the root component (`App`) into the HTML element with the id `root`.

---

### 3. What is the purpose of `package.json`?

**Answer:**
It stores project metadata, dependencies, and scripts used to run, build, and manage the application.

---

### 4. Why shouldn't we edit `node_modules`?

**Answer:**
Because it contains automatically installed third-party packages. Any manual changes can be lost when dependencies are reinstalled.

---

### 5. What is the purpose of the `public` folder?

**Answer:**
It stores static assets that are served directly by the web server without being processed by Vite.

---

# Key Takeaways

* `src/` is where you'll write almost all your React code.
* `main.jsx` is the entry point that renders `App`.
* `App.jsx` is the main application component.
* `index.html` contains the `root` element where React mounts the application.
* `package.json` manages dependencies and project scripts.
* `node_modules` contains installed packages and should not be edited.
* Understanding the application startup flow is essential for debugging and interviews.

---

## Next Topic

**Topic 6: JSX – What It Is, Why React Uses It, and How It Works Internally**. We'll learn why React looks like HTML inside JavaScript, how JSX is transformed behind the scenes, its syntax rules, and build your first JSX examples step by step.
