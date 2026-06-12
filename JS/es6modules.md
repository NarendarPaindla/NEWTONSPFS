# ES6 Modules in JavaScript — Complete Learning (Part 1)

Before ES6, all JavaScript code was usually written inside a single file or loaded through multiple `<script>` tags. As applications grew, managing code became difficult.

**ES6 Modules** solve this problem by allowing us to:

* Split code into multiple files
* Reuse code
* Organize projects properly
* Export functionality from one file
* Import functionality into another file

Think of modules like rooms in a house.

* One room stores clothes
* One room stores food
* One room stores books

Instead of keeping everything in one room.

---

# Why Modules?

Without modules:

```javascript
// file1.js

function add(a, b) {
    return a + b;
}
```

```javascript
// file2.js

function add(a, b) {
    return a + b + 10;
}
```

Both functions have the same name.

When loaded together:

```html
<script src="file1.js"></script>
<script src="file2.js"></script>
```

One function overwrites the other.

This creates:

* Global namespace pollution
* Naming conflicts
* Difficult maintenance

Modules solve this.

---

# Module Terminology

There are only two major concepts.

## 1. Export

Making something available to other files.

```javascript
export
```

---

## 2. Import

Using something from another file.

```javascript
import
```

---

# First Module Example

Project Structure

```text
project/

├── math.js
├── app.js
├── index.html
```

---

# Step 1: Create math.js

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

Here:

```javascript
export
```

means:

> "Allow other files to use this function."

---

# Step 2: Create app.js

```javascript
import { add } from './math.js';

console.log(add(10, 20));
```

Output

```text
30
```

---

# Step 3: Create index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Modules</title>
</head>
<body>

<script type="module" src="app.js"></script>

</body>
</html>
```

Very important:

```html
type="module"
```

Without it:

```javascript
import
export
```

will not work.

---

# Understanding What Happens

When browser loads:

```html
<script type="module" src="app.js"></script>
```

it sees:

```javascript
import { add } from './math.js';
```

Browser then:

1. Loads app.js
2. Finds import
3. Loads math.js
4. Gets add function
5. Executes code

Flow:

```text
app.js
   |
   ↓
math.js
   |
   ↓
returns add()
```

---

# Exporting Multiple Functions

## math.js

```javascript
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export function multiply(a, b) {
    return a * b;
}
```

---

## app.js

```javascript
import { add, subtract, multiply } from './math.js';

console.log(add(10, 5));
console.log(subtract(10, 5));
console.log(multiply(10, 5));
```

Output

```text
15
5
50
```

---

# Alternative Export Style

Instead of writing export repeatedly:

```javascript
export function add(){}
export function sub(){}
export function mul(){}
```

We can do:

## math.js

```javascript
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

function multiply(a, b) {
    return a * b;
}

export { add, subtract, multiply };
```

This exports all functions together.

---

# Visual Representation

```text
math.js

add()
subtract()
multiply()

      |
      |
   export
      |
      ↓

app.js

import
      |
      ↓

use functions
```

---

# Hands-On Practice 1

Create:

## greeting.js

```javascript
export function sayHello(name) {
    return `Hello ${name}`;
}
```

---

## app.js

```javascript
import { sayHello } from './greeting.js';

console.log(sayHello("Narendar"));
```

Output

```text
Hello Narendar
```

---

# Exporting Variables

Modules can export variables too.

## config.js

```javascript
export const PI = 3.14;

export const APP_NAME = "Village Basket";
```

---

## app.js

```javascript
import { PI, APP_NAME } from './config.js';

console.log(PI);
console.log(APP_NAME);
```

Output

```text
3.14
Village Basket
```

---

# Exporting Arrays

## users.js

```javascript
export const users = [
    "Ram",
    "Ravi",
    "Krishna"
];
```

---

## app.js

```javascript
import { users } from './users.js';

console.log(users);
```

Output

```text
["Ram","Ravi","Krishna"]
```

---

# Exporting Objects

## student.js

```javascript
export const student = {
    id: 1,
    name: "Narendar",
    course: "Java"
};
```

---

## app.js

```javascript
import { student } from './student.js';

console.log(student.name);
```

Output

```text
Narendar
```

---

# Important Rule

When importing named exports:

```javascript
import { add } from './math.js';
```

the name must match exactly.

Correct:

```javascript
export function add(){}
```

```javascript
import { add } from './math.js';
```

---

Wrong:

```javascript
import { addition } from './math.js';
```

Error:

```text
addition is not exported
```

---

# Hands-On Practice 2 (Mini Calculator)

### calculator.js

```javascript
export function add(a, b) {
    return a + b;
}

export function sub(a, b) {
    return a - b;
}

export function mul(a, b) {
    return a * b;
}

export function div(a, b) {
    return a / b;
}
```

### app.js

```javascript
import {
    add,
    sub,
    mul,
    div
} from './calculator.js';

console.log(add(10, 5));
console.log(sub(10, 5));
console.log(mul(10, 5));
console.log(div(10, 5));
```

Output

```text
15
5
50
2
```

---



# ES6 Modules in JavaScript — Part 2

In Part 1 we learned:

* export
* import
* named exports
* exporting variables, arrays, objects

Now we will learn the most important module concepts used in real projects.

---

# Default Export

Sometimes a file contains one main thing.

Instead of:

```javascript
export function add(){}
```

we can export it as the default export.

## Syntax

```javascript
export default something;
```

---

# Example 1

## math.js

```javascript
function add(a, b) {
    return a + b;
}

export default add;
```

---

## app.js

```javascript
import add from './math.js';

console.log(add(10, 20));
```

Output

```text
30
```

Notice:

```javascript
import add from './math.js';
```

No curly braces.

---

# Why No Curly Braces?

Named export:

```javascript
export function add(){}
```

Import:

```javascript
import { add } from './math.js';
```

Uses curly braces.

---

Default export:

```javascript
export default add;
```

Import:

```javascript
import add from './math.js';
```

No curly braces.

---

# Visual Difference

Named Export

```text
math.js
--------
add()

export add

app.js
--------
import { add }
```

---

Default Export

```text
math.js
--------
export default add

app.js
--------
import add
```

---

# Default Export with Anonymous Function

## math.js

```javascript
export default function(a, b) {
    return a + b;
}
```

---

## app.js

```javascript
import add from './math.js';

console.log(add(10, 20));
```

Output

```text
30
```

---

# Default Export with Class

## User.js

```javascript
export default class User {

    constructor(name) {
        this.name = name;
    }

    display() {
        console.log(this.name);
    }

}
```

---

## app.js

```javascript
import User from './User.js';

const user = new User("Narendar");

user.display();
```

Output

```text
Narendar
```

---

# Important Rule

One file can have only one default export.

Valid:

```javascript
export default function(){}
```

or

```javascript
export default class{}
```

---

Invalid:

```javascript
export default function(){}

export default class{}
```

Error.

---

# Named Export + Default Export Together

A file can contain:

* Multiple named exports
* One default export

---

## calculator.js

```javascript
export function add(a, b) {
    return a + b;
}

export function sub(a, b) {
    return a - b;
}

function multiply(a, b) {
    return a * b;
}

export default multiply;
```

---

## app.js

```javascript
import multiply, {
    add,
    sub
} from './calculator.js';

console.log(add(10, 5));
console.log(sub(10, 5));
console.log(multiply(10, 5));
```

Output

```text
15
5
50
```

---

# Understanding This Import

```javascript
import multiply,
{
    add,
    sub
}
from './calculator.js';
```

Default export:

```javascript
multiply
```

Named exports:

```javascript
add
sub
```

---

# Renaming Imports (Alias)

Sometimes names clash.

Example:

## math.js

```javascript
export function add(a, b) {
    return a + b;
}
```

---

Import with new name:

```javascript
import { add as addition } from './math.js';

console.log(addition(10, 20));
```

Output

```text
30
```

---

# Why Alias?

Suppose:

```javascript
function add() {}
```

already exists.

Importing another add:

```javascript
import { add } from './math.js';
```

creates conflict.

Solution:

```javascript
import { add as addition } from './math.js';
```

---

# Export Alias

You can rename during export.

## math.js

```javascript
function add(a, b) {
    return a + b;
}

export {
    add as addition
};
```

---

## app.js

```javascript
import { addition } from './math.js';

console.log(addition(10, 20));
```

Output

```text
30
```

---

# Import Everything

Sometimes a module contains many exports.

Instead of:

```javascript
import {
    add,
    sub,
    mul,
    div
}
from './math.js';
```

Use:

```javascript
import * as math from './math.js';
```

---

# Example

## math.js

```javascript
export function add(a, b) {
    return a + b;
}

export function sub(a, b) {
    return a - b;
}

export function mul(a, b) {
    return a * b;
}
```

---

## app.js

```javascript
import * as math from './math.js';

console.log(math.add(10, 5));
console.log(math.sub(10, 5));
console.log(math.mul(10, 5));
```

Output

```text
15
5
50
```

---

# Visual Representation

```text
math.js
---------
add()
sub()
mul()

     |
     |
import * as math
     |
     ↓

math.add()
math.sub()
math.mul()
```

---

# Hands-On Project

Create a utility module.

---

## utils.js

```javascript
export function capitalize(text) {
    return text.toUpperCase();
}

export function reverse(text) {
    return text.split('').reverse().join('');
}

export function length(text) {
    return text.length;
}
```

---

## app.js

```javascript
import * as utils from './utils.js';

console.log(utils.capitalize("javascript"));

console.log(utils.reverse("hello"));

console.log(utils.length("coding"));
```

Output

```text
JAVASCRIPT
olleh
6
```

---

# Module Scope

One of the biggest advantages of modules.

Variables inside modules are private.

---

## math.js

```javascript
const secret = 100;

export function add(a, b) {
    return a + b;
}
```

---

## app.js

```javascript
import { add } from './math.js';

console.log(secret);
```

Error:

```text
secret is not defined
```

---

Because:

```javascript
const secret = 100;
```

belongs only to:

```javascript
math.js
```

---

# Traditional Script

```javascript
var name = "JavaScript";
```

Accessible globally.

---

# Module

```javascript
const name = "JavaScript";
```

Accessible only inside module.

Safer.

---

# Real Project Structure

```text
project/

src/

├── app.js
├── services/
│     └── api.js
│
├── utils/
│     ├── math.js
│     └── string.js
│
├── models/
│     └── user.js
│
└── config/
      └── config.js
```

Large applications use modules exactly like this.

Every folder contains its own responsibility.

---

# Practice Project

Create three files.

---

## math.js

```javascript
export function square(n) {
    return n * n;
}

export function cube(n) {
    return n * n * n;
}
```

---

## message.js

```javascript
export default function() {
    return "Welcome to ES6 Modules";
}
```

---

## app.js

```javascript
import welcome from './message.js';

import {
    square,
    cube
} from './math.js';

console.log(welcome());

console.log(square(5));

console.log(cube(5));
```

Output

```text
Welcome to ES6 Modules
25
125
```

---

# ES6 Modules in JavaScript — Part 3 (Advanced Concepts)

Now that you understand:

* Named Exports
* Default Exports
* Import Everything (`*`)
* Aliasing (`as`)
* Module Scope

Let's move deeper into how modules work internally and how real-world projects organize them.

---

# Re-Exporting Modules

Suppose we have multiple module files.

```text
utils/

├── math.js
├── string.js
└── array.js
```

---

## math.js

```javascript
export function add(a, b) {
    return a + b;
}

export function multiply(a, b) {
    return a * b;
}
```

---

## string.js

```javascript
export function capitalize(text) {
    return text.toUpperCase();
}
```

---

## array.js

```javascript
export function first(arr) {
    return arr[0];
}
```

Without re-exporting:

```javascript
import { add } from './utils/math.js';
import { capitalize } from './utils/string.js';
import { first } from './utils/array.js';
```

Too many imports.

---

# Solution: Re-Export

Create:

## index.js

```javascript
export * from './math.js';
export * from './string.js';
export * from './array.js';
```

---

Now:

```javascript
import {
    add,
    capitalize,
    first
}
from './utils/index.js';
```

Much cleaner.

---

# Why Use index.js?

This pattern is called a:

```text
Barrel File
```

Because it gathers exports into one place.

---

# Real Project Example

```text
components/

├── Button.js
├── Card.js
├── Navbar.js
└── index.js
```

---

## index.js

```javascript
export { default as Button } from './Button.js';

export { default as Card } from './Card.js';

export { default as Navbar } from './Navbar.js';
```

---

Usage:

```javascript
import {
    Button,
    Card,
    Navbar
}
from './components';
```

Very common in:

* React
* Next.js
* Angular
* Vue

---

# Understanding Module Loading

Consider:

## math.js

```javascript
console.log("Math Loaded");

export function add(a, b) {
    return a + b;
}
```

---

## app.js

```javascript
import { add } from './math.js';

console.log("App Loaded");
```

Output

```text
Math Loaded
App Loaded
```

---

Why?

Because browser loads dependencies first.

Flow:

```text
app.js
  |
  ↓
math.js loaded
  |
  ↓
execute math.js
  |
  ↓
execute app.js
```

---

# Module Loading Graph

Example:

```text
app.js
 |
 ├── user.js
 |
 ├── math.js
 |
 └── api.js
       |
       └── config.js
```

Browser builds a dependency graph.

```text
Dependency Graph
```

This is how browser knows:

```text
Load config.js first
Load api.js
Load user.js
Load math.js
Load app.js
```

---

# Modules Execute Only Once

This is very important.

---

## counter.js

```javascript
console.log("Counter Module Loaded");

export const count = 100;
```

---

## app.js

```javascript
import { count } from './counter.js';
import { count as c } from './counter.js';

console.log(count);
console.log(c);
```

Output

```text
Counter Module Loaded
100
100
```

Notice:

```text
Counter Module Loaded
```

appears only once.

---

Browser caches modules.

---

# Module Caching

Imagine:

```javascript
import './math.js';
import './math.js';
import './math.js';
```

Browser executes:

```javascript
math.js
```

only once.

Then reuses the result.

---

# Live Bindings

One of the most powerful ES6 module features.

---

## counter.js

```javascript
export let count = 0;

export function increment() {
    count++;
}
```

---

## app.js

```javascript
import {
    count,
    increment
}
from './counter.js';

console.log(count);

increment();

console.log(count);
```

Output

```text
0
1
```

---

Why?

Because imports are not copies.

They are:

```text
Live References
```

---

Traditional thinking:

```text
count = copied value
```

Wrong.

ES6:

```text
count = reference
```

Like a pointer.

---

Visual:

```text
counter.js

count -----> 0

increment()

count -----> 1

app.js sees same value
```

---

# Dynamic Import

Until now:

```javascript
import { add } from './math.js';
```

called:

```text
Static Import
```

Loaded immediately.

---

Sometimes we want:

```text
Load module only when needed
```

Example:

* User clicks button
* Then load module

This is:

```text
Dynamic Import
```

---

Syntax

```javascript
import('./math.js')
```

Returns a Promise.

---

# Example

## math.js

```javascript
export function add(a, b) {
    return a + b;
}
```

---

## app.js

```javascript
button.addEventListener('click', async () => {

    const math = await import('./math.js');

    console.log(
        math.add(10, 20)
    );

});
```

Output after click:

```text
30
```

---

# Understanding Dynamic Import

Normal Import

```javascript
import { add } from './math.js';
```

Load immediately.

---

Dynamic Import

```javascript
import('./math.js');
```

Load later.

---

Flow:

```text
User Opens Page
      |
      ↓
Module NOT Loaded
      |
User Clicks Button
      |
      ↓
Module Loaded
      |
      ↓
Use Function
```

---

# Why Dynamic Import?

Suppose website contains:

```text
Dashboard
Reports
Analytics
Charts
Maps
AI Features
```

Loading everything initially:

```text
5 MB JavaScript
```

Slow.

---

Instead:

```text
Load Dashboard First
```

When user clicks Analytics:

```text
Load Analytics Module
```

Faster.

---

# Code Splitting

Dynamic imports enable:

```text
Code Splitting
```

Meaning:

Instead of:

```text
One Huge Bundle
```

You get:

```text
dashboard.js

analytics.js

charts.js

maps.js
```

Loaded only when required.

---

Visual

Without Splitting

```text
main.js

10 MB
```

---

With Splitting

```text
main.js

1 MB
```

Later:

```text
analytics.js
```

loads when needed.

---

# Hands-On Dynamic Import Example

## message.js

```javascript
export function showMessage() {
    console.log("Module Loaded");
}
```

---

## app.js

```javascript
const btn =
document.querySelector("button");

btn.addEventListener(
    "click",
    async () => {

        const module =
        await import('./message.js');

        module.showMessage();

    }
);
```

---

## HTML

```html
<button>
Load Module
</button>

<script
type="module"
src="app.js">
</script>
```

Output after clicking:

```text
Module Loaded
```

---

# Static vs Dynamic Import

| Feature            | Static | Dynamic          |
| ------------------ | ------ | ---------------- |
| Loaded Immediately | Yes    | No               |
| Returns Promise    | No     | Yes              |
| Lazy Loading       | No     | Yes              |
| Code Splitting     | No     | Yes              |
| Most Common        | Yes    | Used when needed |

---

# Complete Flow of ES6 Modules

```text
Module Created
      |
      ↓
Export Something
      |
      ↓
Import Somewhere
      |
      ↓
Dependency Graph Built
      |
      ↓
Modules Loaded
      |
      ↓
Executed Once
      |
      ↓
Cached
      |
      ↓
Shared Through Live Bindings
```

---



