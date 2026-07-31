# Module 1 – Introduction to React

# Topic 4: Setting Up React with Vite

In this lesson, we are going to create our **first React application**.

However, before writing the first React component, you need to understand **why we use Vite**.

Most beginners directly type the commands without knowing what they do. In an interview, if someone asks **"Why do you use Vite?"**, they cannot answer.

Today, we'll understand everything from scratch.

---

# Learning Objectives

By the end of this lesson, you will understand:

* What Vite is
* Why React needs a build tool
* Why Create React App became outdated
* Why Vite is faster
* Installing Node.js
* Creating your first React project
* Running the development server
* Understanding every generated file
* Hands-on activity

---

# Step 1: Why Can't We Just Create an HTML File?

Suppose you write this.

```html
<!DOCTYPE html>
<html>
<head>
    <title>React</title>
</head>
<body>

<div id="root"></div>

<script>

function App(){
    return "<h1>Hello React</h1>";
}

</script>

</body>
</html>
```

Will this work like a React application?

No.

Why?

Because React is **not built into the browser**.

The browser understands:

* HTML
* CSS
* JavaScript

It does **not** understand:

* JSX
* React Components
* React Hooks
* ES Modules (without tooling in many workflows)
* Optimized production builds

We need tools that prepare our React code for the browser.

---

# What Does a Build Tool Do?

Imagine writing a book.

You write it in Microsoft Word.

Can a printing machine print the Word document directly?

Usually, it is converted into PDF first.

Similarly,

You write:

```jsx
function App() {
    return <h1>Hello</h1>;
}
```

The browser cannot understand JSX directly.

A build tool converts it into JavaScript the browser understands.

---

# What is Vite?

**Definition**

Vite (pronounced **"veet"**, from the French word for "fast") is a modern frontend build tool and development server that provides extremely fast startup, instant updates during development, and optimized production builds.

Think of Vite as the **assistant** that prepares your React application for development and deployment.

---

# Why Was Vite Created?

Earlier, React projects were commonly created using **Create React App (CRA)**.

Command:

```bash
npx create-react-app my-app
```

It worked well for small projects.

But as applications became larger:

* Initial startup became slower.
* Hot reloads took longer.
* Build times increased.
* Configuration became harder to customize.

Developers wanted a faster experience.

That led to tools like **Vite**.

---

# Create React App vs Vite

| Feature           | Create React App | Vite                                     |
| ----------------- | ---------------- | ---------------------------------------- |
| Startup Speed     | Slow             | Very Fast                                |
| Hot Reload        | Slower           | Instant (HMR)                            |
| Build Tool        | Webpack          | Rollup (build) + Native ES Modules (dev) |
| Configuration     | More complex     | Simpler                                  |
| Recommended Today | Generally No     | Yes                                      |

Today, most new React projects use **Vite**.

---

# Why Is Vite Fast?

Traditional workflow:

```text
Start Project
      ↓
Bundle Entire Project
      ↓
Start Server
```

Even if you open one page, everything is prepared first.

Vite's development workflow:

```text
Start Server
      ↓
Open Browser
      ↓
Load Only Required Files
```

It serves files on demand using native ES Modules during development.

---

# Hot Module Replacement (HMR)

Suppose your app contains:

```text
Navbar

Sidebar

Footer

Profile

Dashboard
```

You change only the Footer.

Without HMR:

```text
Save File
      ↓
Reload Entire Page
```

With HMR:

```text
Save File
      ↓
Only Footer Updates
```

This gives a much smoother development experience.

---

# Step 2: Install Node.js

React projects require **Node.js**.

Node.js provides:

* npm (Node Package Manager)
* The environment needed to install project dependencies

Verify the installation.

Open Terminal or Command Prompt.

```bash
node -v
```

Example:

```text
v22.18.0
```

Check npm:

```bash
npm -v
```

Example:

```text
10.8.2
```

If both commands work, you're ready.

---

# Step 3: Create Your First React Project

Open Terminal.

Run:

```bash
npm create vite@latest
```

The terminal will ask:

```text
Project name:
```

Type:

```text
my-first-react-app
```

Next:

```text
Select a framework:
```

Choose:

```text
React
```

Next:

```text
Select a variant:
```

Choose:

```text
JavaScript
```

(We'll start with JavaScript first. Later we'll learn TypeScript.)

---

# Step 4: Move into the Project

```bash
cd my-first-react-app
```

---

# Step 5: Install Dependencies

```bash
npm install
```

This downloads everything React needs.

You'll notice a new folder:

```text
node_modules/
```

Don't worry—we'll understand this folder in the next lesson.

---

# Step 6: Run the Development Server

```bash
npm run dev
```

You'll see output similar to:

```text
VITE v7.x.x

Local:
http://localhost:5173/
```

Open:

```text
http://localhost:5173
```

Congratulations! 🎉

You've created your first React application.

---

# What Happens Internally?

When you run:

```bash
npm run dev
```

Vite:

1. Starts a local development server.
2. Reads your project files.
3. Serves them to the browser.
4. Watches for file changes.
5. Updates only the changed modules using HMR.

---

# Folder Structure

After creating the project, you'll see something like:

```text
my-first-react-app/
│
├── node_modules/
├── public/
├── src/
├── .gitignore
├── index.html
├── package.json
├── package-lock.json
├── vite.config.js
└── README.md
```

Don't worry about each file yet.

We'll study them one by one in the next lesson.

---

# Real-World Analogy

Imagine you're writing a book.

Without Vite:

```text
Write one sentence
        ↓
Print the entire book again
```

With Vite:

```text
Write one sentence
        ↓
Update only that page
```

This is why development feels much faster.

---

# Hands-on Activity

1. Install Node.js if it isn't already installed.
2. Create a React project using Vite.
3. Run the project with:

```bash
npm run dev
```

4. Open the browser at:

```text
http://localhost:5173
```

5. Stop the server using:

```bash
Ctrl + C
```

6. Start it again using:

```bash
npm run dev
```

Become comfortable with these commands—they're part of your daily React workflow.

---

# Interview Questions

### 1. What is Vite?

**Answer:**
Vite is a modern frontend build tool and development server that provides fast startup, Hot Module Replacement (HMR), and optimized production builds.

---

### 2. Why do we use Vite with React?

**Answer:**
React uses JSX and modern JavaScript features that browsers don't execute directly in the same development workflow. Vite provides a development server, processes the code, supports HMR, and creates optimized production builds.

---

### 3. Why is Vite faster than Create React App?

**Answer:**
Vite serves modules on demand using native ES Modules during development instead of bundling the entire application before starting. This results in much faster startup and updates.

---

### 4. What command creates a Vite project?

```bash
npm create vite@latest
```

---

### 5. Which command starts the React development server?

```bash
npm run dev
```

---

# Key Takeaways

* React projects typically use **Vite** as the build tool.
* Vite offers **fast startup**, **Hot Module Replacement**, and **optimized builds**.
* A React project requires **Node.js** and **npm**.
* You can create a new project with `npm create vite@latest`.
* Start the development server with `npm run dev`.

---

## Next Topic

**Topic 5: Understanding the React Project Structure** — we'll explore every generated file (`src`, `public`, `package.json`, `node_modules`, `index.html`, `vite.config.js`, and more), explain why each exists, and see how they work together in a real React application.
