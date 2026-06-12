# JavaScript Event Listeners — Part 1 (Foundation)

Event Listeners are one of the most important concepts in JavaScript.

Without Event Listeners:

```html
User clicks button
Nothing happens
```

With Event Listeners:

```html
User clicks button
↓
JavaScript reacts
↓
Action happens
```

Almost every interactive website uses event listeners.

Examples:

* Button Click
* Form Submit
* Mouse Hover
* Keyboard Press
* Scrolling
* Drag and Drop
* Touch Events
* Window Resize

All of these are handled using Event Listeners.

---

# What is an Event?

An Event is an action that occurs in the browser.

Examples:

```text
Clicking a button

Typing in input field

Moving mouse

Pressing keyboard key

Scrolling page

Submitting form

Loading page
```

---

## Real Life Example

Imagine a doorbell.

```text
Door Bell
     |
     |
Someone Presses
     |
     ↓
Sound Occurs
```

Here:

```text
Pressing Bell = Event

Sound = Response
```

Same in JavaScript:

```text
Button Click = Event

Function Execution = Response
```

---

# First Event Listener

HTML

```html
<button id="btn">
    Click Me
</button>
```

JavaScript

```javascript
const button =
document.getElementById("btn");

button.addEventListener(
    "click",
    function () {
        console.log("Button Clicked");
    }
);
```

Output when clicked:

```text
Button Clicked
```

---

# Understanding addEventListener()

Syntax

```javascript
element.addEventListener(
    event,
    callbackFunction
);
```

---

Example

```javascript
button.addEventListener(
    "click",
    function () {
        console.log("Clicked");
    }
);
```

---

Breakdown

```javascript
button
```

Element we are listening to.

---

```javascript
"click"
```

Event type.

---

```javascript
function () {
    console.log("Clicked");
}
```

Function executed when event occurs.

---

Visual Flow

```text
User Clicks Button
        |
        ↓
Browser Detects Event
        |
        ↓
Event Listener Activated
        |
        ↓
Callback Function Runs
```

---

# Why Use Callback Function?

Observe:

```javascript
function greet() {
    console.log("Hello");
}
```

Wrong:

```javascript
button.addEventListener(
    "click",
    greet()
);
```

Output:

```text
Hello
```

Runs immediately.

Not on click.

---

Correct:

```javascript
button.addEventListener(
    "click",
    greet
);
```

Now:

```text
Function waits

User clicks

Function executes
```

---

# Example

HTML

```html
<button id="btn">
    Click
</button>
```

JavaScript

```javascript
function greet() {
    console.log("Welcome");
}

const button =
document.getElementById("btn");

button.addEventListener(
    "click",
    greet
);
```

Output:

```text
Welcome
```

after click.

---

# Anonymous Functions

Most common approach.

```javascript
button.addEventListener(
    "click",
    function () {
        console.log("Clicked");
    }
);
```

---

Why?

Used only once.

No need to create separate function.

---

# Arrow Functions

Modern JavaScript.

```javascript
button.addEventListener(
    "click",
    () => {
        console.log("Clicked");
    }
);
```

Same output.

---

# Hands-On Example 1

HTML

```html
<button id="helloBtn">
    Say Hello
</button>
```

JavaScript

```javascript
const btn =
document.getElementById("helloBtn");

btn.addEventListener(
    "click",
    () => {
        alert("Hello User");
    }
);
```

Click:

```text
Popup Appears
```

---

# Changing Text on Click

HTML

```html
<h1 id="heading">
    Welcome
</h1>

<button id="btn">
    Change
</button>
```

---

JavaScript

```javascript
const heading =
document.getElementById("heading");

const button =
document.getElementById("btn");

button.addEventListener(
    "click",
    () => {
        heading.textContent =
        "JavaScript Event Listener";
    }
);
```

Before Click:

```text
Welcome
```

After Click:

```text
JavaScript Event Listener
```

---

# Changing CSS on Click

HTML

```html
<div id="box"></div>

<button id="btn">
    Change Color
</button>
```

CSS

```css
#box{
    width:200px;
    height:200px;
    background:red;
}
```

---

JavaScript

```javascript
const box =
document.getElementById("box");

const button =
document.getElementById("btn");

button.addEventListener(
    "click",
    () => {
        box.style.background =
        "green";
    }
);
```

Output:

```text
Red Box
↓ Click
Green Box
```

---

# Multiple Event Listeners

One element can listen to multiple events.

HTML

```html
<button id="btn">
    Hover Me
</button>
```

JavaScript

```javascript
const btn =
document.getElementById("btn");

btn.addEventListener(
    "click",
    () => {
        console.log("Clicked");
    }
);

btn.addEventListener(
    "mouseenter",
    () => {
        console.log("Mouse Enter");
    }
);
```

---

Possible Output

```text
Mouse Enter
Clicked
```

---

# Common Events

## Click Event

```javascript
button.addEventListener(
    "click",
    () => {}
);
```

Triggered when clicked.

---

## Double Click Event

```javascript
button.addEventListener(
    "dblclick",
    () => {}
);
```

Triggered on double click.

---

## Mouse Enter

```javascript
button.addEventListener(
    "mouseenter",
    () => {}
);
```

Triggered when mouse enters.

---

## Mouse Leave

```javascript
button.addEventListener(
    "mouseleave",
    () => {}
);
```

Triggered when mouse leaves.

---

# Practical Example

HTML

```html
<div id="box"></div>
```

CSS

```css
#box{
    width:200px;
    height:200px;
    background:red;
}
```

JavaScript

```javascript
const box =
document.getElementById("box");

box.addEventListener(
    "mouseenter",
    () => {
        box.style.background =
        "green";
    }
);

box.addEventListener(
    "mouseleave",
    () => {
        box.style.background =
        "red";
    }
);
```

Behavior:

```text
Mouse Enter → Green

Mouse Leave → Red
```

---

# Event Listener vs HTML onclick

Old Method

```html
<button onclick="show()">
    Click
</button>
```

JavaScript

```javascript
function show() {
    alert("Hello");
}
```

Works.

But not recommended for modern projects.

---

Modern Method

```javascript
button.addEventListener(
    "click",
    show
);
```

Advantages:

```text
Cleaner code

Separation of HTML and JS

Multiple listeners possible

Easy maintenance
```

---

# Hands-On Mini Project

HTML

```html
<h1 id="count">
    0
</h1>

<button id="increase">
    Increase
</button>
```

JavaScript

```javascript
let count = 0;

const heading =
document.getElementById("count");

const button =
document.getElementById("increase");

button.addEventListener(
    "click",
    () => {

        count++;

        heading.textContent =
        count;

    }
);
```

Output:

```text
0

Click

1

Click

2

Click

3
```

---

# Mental Model

Whenever you write:

```javascript
element.addEventListener(
    "event",
    callback
);
```

Think:

```text
Listen to this element

Wait for event

When event occurs

Run callback function
```

Flow:

```text
Element
   |
   ↓
Event Listener
   |
Waits...
   |
Event Happens
   |
Callback Executes
```
* Real-time Form Validation Project
* Building a Complete Interactive UI using Event Listeners.


# JavaScript Event Listeners — Part 2 (Event Object, Keyboard, Input, Form Events)

In Part 1, we learned:

* What Events are
* `addEventListener()`
* Click Events
* Mouse Events
* Callback Functions

Now we'll learn something extremely important.

---

# What is the Event Object?

Whenever an event occurs, JavaScript automatically creates an object containing information about that event.

Example:

```text
Button Clicked
```

JavaScript creates:

```javascript
{
   type: "click",
   target: button,
   currentTarget: button,
   ...
}
```

This object is called:

```text
Event Object
```

---

# Receiving the Event Object

HTML

```html
<button id="btn">
    Click Me
</button>
```

JavaScript

```javascript
const button =
document.getElementById("btn");

button.addEventListener(
    "click",
    function(event){

        console.log(event);

    }
);
```

When clicked:

```text
PointerEvent {...}
```

A huge object appears in console.

---

# Common Names

All are same.

```javascript
event
```

```javascript
e
```

```javascript
evt
```

Most developers use:

```javascript
e
```

Example:

```javascript
button.addEventListener(
    "click",
    (e) => {

        console.log(e);

    }
);
```

---

# event.type

Returns event name.

```javascript
button.addEventListener(
    "click",
    (e) => {

        console.log(e.type);

    }
);
```

Output

```text
click
```

---

Mouse Enter Example

```javascript
button.addEventListener(
    "mouseenter",
    (e) => {

        console.log(e.type);

    }
);
```

Output

```text
mouseenter
```

---

# event.target

Very important.

Returns the actual element that triggered the event.

HTML

```html
<button id="btn">
    Submit
</button>
```

JavaScript

```javascript
button.addEventListener(
    "click",
    (e) => {

        console.log(e.target);

    }
);
```

Output

```html
<button id="btn">
    Submit
</button>
```

---

Visual

```text
User Clicks Button
      |
      ↓
event.target
      |
      ↓
Button Element
```

---

# Changing Clicked Element

HTML

```html
<button>
    Click Me
</button>
```

JavaScript

```javascript
const button =
document.querySelector("button");

button.addEventListener(
    "click",
    (e) => {

        e.target.textContent =
        "Clicked";

    }
);
```

Before

```text
Click Me
```

After

```text
Clicked
```

---

# event.target vs Variable

Normal

```javascript
button.textContent =
"Clicked";
```

Using Event Object

```javascript
e.target.textContent =
"Clicked";
```

Both work.

Second is more flexible.

---

# event.currentTarget

Consider:

```javascript
button.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.currentTarget
        );

    }
);
```

Output:

```text
button element
```

Usually same as listener element.

Later during Event Bubbling you'll see the difference.

---

# Keyboard Events

Keyboard events occur when user presses keys.

Common events:

```text
keydown

keyup
```

---

# keydown

Triggered when key is pressed.

HTML

```html
<input type="text">
```

JavaScript

```javascript
const input =
document.querySelector("input");

input.addEventListener(
    "keydown",
    () => {

        console.log("Key Pressed");

    }
);
```

Every key press prints:

```text
Key Pressed
```

---

# keyup

Triggered when key is released.

```javascript
input.addEventListener(
    "keyup",
    () => {

        console.log("Released");

    }
);
```

---

Visual

```text
Press Key
   |
keydown

Release Key
   |
keyup
```

---

# Which Key Was Pressed?

Use:

```javascript
e.key
```

Example

```javascript
input.addEventListener(
    "keydown",
    (e)=>{

        console.log(e.key);

    }
);
```

If user presses:

```text
A
```

Output

```text
a
```

---

If user presses:

```text
Enter
```

Output

```text
Enter
```

---

# Keyboard Project

HTML

```html
<h1 id="result">
    Press Any Key
</h1>
```

JavaScript

```javascript
const heading =
document.getElementById("result");

document.addEventListener(
    "keydown",
    (e)=>{

        heading.textContent =
        `You Pressed ${e.key}`;

    }
);
```

Example Output

```text
You Pressed A

You Pressed Enter

You Pressed Space
```

---

# Input Events

Very important.

Used in:

```text
Search Bars

Forms

Live Validation

Chat Applications
```

---

HTML

```html
<input
type="text"
id="name"
/>
```

---

# input Event

Triggered whenever value changes.

JavaScript

```javascript
const input =
document.getElementById("name");

input.addEventListener(
    "input",
    ()=>{

        console.log(
            input.value
        );

    }
);
```

If user types:

```text
Hello
```

Output

```text
H
He
Hel
Hell
Hello
```

---

# Using Event Object

```javascript
input.addEventListener(
    "input",
    (e)=>{

        console.log(
            e.target.value
        );

    }
);
```

Output same.

---

# Live Text Display Project

HTML

```html
<input
type="text"
id="name"
/>

<h2 id="output">
</h2>
```

JavaScript

```javascript
const input =
document.getElementById("name");

const output =
document.getElementById("output");

input.addEventListener(
    "input",
    (e)=>{

        output.textContent =
        e.target.value;

    }
);
```

Typing:

```text
JavaScript
```

Displays:

```text
JavaScript
```

in real time.

---

# Focus Event

Occurs when input becomes active.

HTML

```html
<input id="name">
```

JavaScript

```javascript
input.addEventListener(
    "focus",
    ()=>{

        console.log(
            "Input Focused"
        );

    }
);
```

Output when clicked:

```text
Input Focused
```

---

# Blur Event

Occurs when input loses focus.

```javascript
input.addEventListener(
    "blur",
    ()=>{

        console.log(
            "Input Lost Focus"
        );

    }
);
```

Output

```text
Input Lost Focus
```

---

Visual

```text
Click Input
    |
    ↓
Focus

Click Outside
    |
    ↓
Blur
```

---

# Form Submit Event

One of the most important events.

HTML

```html
<form id="myForm">

<input type="text">

<button>
Submit
</button>

</form>
```

---

JavaScript

```javascript
const form =
document.getElementById("myForm");

form.addEventListener(
    "submit",
    ()=>{

        console.log(
            "Form Submitted"
        );

    }
);
```

---

# Problem

When form submits:

```text
Page Reloads
```

Why?

Because browser default behavior is:

```text
Submit Form
↓
Reload Page
```

---

# preventDefault()

Stops default browser behavior.

```javascript
form.addEventListener(
    "submit",
    (e)=>{

        e.preventDefault();

        console.log(
            "Submitted"
        );

    }
);
```

Now page does NOT reload.

---

# Understanding preventDefault()

Without:

```text
Submit
 ↓
Reload
```

With:

```text
Submit
 ↓
preventDefault()
 ↓
No Reload
```

---

# Real Form Example

HTML

```html
<form id="form">

<input
type="text"
id="username"
/>

<button>
Submit
</button>

</form>
```

JavaScript

```javascript
const form =
document.getElementById("form");

const username =
document.getElementById("username");

form.addEventListener(
    "submit",
    (e)=>{

        e.preventDefault();

        console.log(
            username.value
        );

    }
);
```

If user enters:

```text
Narendar
```

Output

```text
Narendar
```

without page refresh.

---

# Event Flow So Far

```text
User Action
      |
      ↓
Event Occurs
      |
      ↓
Browser Creates Event Object
      |
      ↓
Listener Receives Event Object
      |
      ↓
Callback Executes
```

Example:

```javascript
input.addEventListener(
    "input",
    (e)=>{

        console.log(
            e.target.value
        );

    }
);
```

Flow:

```text
User Types
      |
      ↓
input Event
      |
      ↓
Event Object Created
      |
      ↓
e.target.value
      |
      ↓
Display Value
```

---
# JavaScript Event Listeners — Part 3 (Event Bubbling, Capturing, removeEventListener, Event Delegation)

This is one of the most important parts of JavaScript.

Most beginners understand:

```javascript
button.addEventListener("click", () => {});
```

But they don't understand:

```text
How event travels
Why parent event triggers
How event delegation works
How large applications handle thousands of elements
```

Let's learn deeply.

---

# Removing Event Listeners

Until now:

```javascript
button.addEventListener(
    "click",
    greet
);
```

adds an event listener.

Sometimes we want:

```text
Add Listener

↓

Use Listener

↓

Remove Listener
```

For this we use:

```javascript
removeEventListener()
```

---

# Example

HTML

```html
<button id="btn">
    Click
</button>
```

JavaScript

```javascript
const button =
document.getElementById("btn");

function greet() {
    console.log("Hello");
}

button.addEventListener(
    "click",
    greet
);

button.removeEventListener(
    "click",
    greet
);
```

Now clicking:

```text
Nothing Happens
```

---

# Important Rule

This works:

```javascript
function greet() {
    console.log("Hello");
}

button.addEventListener(
    "click",
    greet
);

button.removeEventListener(
    "click",
    greet
);
```

---

This DOES NOT work:

```javascript
button.addEventListener(
    "click",
    function(){
        console.log("Hello");
    }
);

button.removeEventListener(
    "click",
    function(){
        console.log("Hello");
    }
);
```

Why?

Because:

```text
Two Different Functions
```

Even though code looks same.

Memory:

```text
Function A

≠

Function B
```

---

# Practical Example

Disable button after first click.

HTML

```html
<button id="btn">
    Click
</button>
```

JavaScript

```javascript
const button =
document.getElementById("btn");

function showMessage() {

    console.log("Clicked");

    button.removeEventListener(
        "click",
        showMessage
    );

}

button.addEventListener(
    "click",
    showMessage
);
```

Output

```text
First Click
Clicked

Second Click
Nothing

Third Click
Nothing
```

---

# Understanding DOM Tree

Consider:

```html
<div id="grandparent">

    <div id="parent">

        <button id="child">
            Click
        </button>

    </div>

</div>
```

DOM Tree:

```text
grandparent
     |
     |
   parent
     |
     |
   child
```

or

```text
HTML
 |
BODY
 |
grandparent
 |
parent
 |
button
```

---

# Event Bubbling

One of the most important JavaScript concepts.

Suppose:

```html
<div id="parent">

    <button id="child">
        Click
    </button>

</div>
```

JavaScript

```javascript
const parent =
document.getElementById("parent");

const child =
document.getElementById("child");

parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    }
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

---

Question:

When button is clicked, what prints?

Many beginners think:

```text
Child
```

Wrong.

Output:

```text
Child
Parent
```

---

# Why?

Because events bubble upward.

Flow:

```text
User Clicks Button
        |
        ↓
Child Executes
        |
        ↓
Parent Executes
```

This is called:

```text
Event Bubbling
```

---

Visual

```text
grandparent
     ↑
parent
     ↑
child
```

Event travels:

```text
child

↑

parent

↑

grandparent
```

---

# Bigger Example

HTML

```html
<div id="grandparent">

    <div id="parent">

        <button id="child">
            Click
        </button>

    </div>

</div>
```

JavaScript

```javascript
grandparent.addEventListener(
    "click",
    ()=>{

        console.log("Grandparent");

    }
);

parent.addEventListener(
    "click",
    ()=>{

        console.log("Parent");

    }
);

child.addEventListener(
    "click",
    ()=>{

        console.log("Child");

    }
);
```

Click button.

Output

```text
Child
Parent
Grandparent
```

---

Flow

```text
Button Click

↓

Child

↓

Parent

↓

Grandparent
```

---

# stopPropagation()

Sometimes we want:

```text
Child Executes

Parent Should NOT Execute
```

Use:

```javascript
e.stopPropagation()
```

---

Example

```javascript
child.addEventListener(
    "click",
    (e)=>{

        e.stopPropagation();

        console.log("Child");

    }
);
```

Parent Listener

```javascript
parent.addEventListener(
    "click",
    ()=>{

        console.log("Parent");

    }
);
```

Output

```text
Child
```

Only.

---

Visual

Without stopPropagation

```text
Child
 ↑
Parent
 ↑
Grandparent
```

---

With stopPropagation

```text
Child

❌ Stop

Parent never receives
```

---

# Event Capturing

Until now:

```text
Child
↓
Parent
↓
Grandparent
```

Actually that's bubbling.

There is another phase:

```text
Capturing
```

---

Normal Event Flow

```text
Capturing Phase

↓

Target Phase

↓

Bubbling Phase
```

---

Visual

```text
Grandparent
    ↓
Parent
    ↓
Child

(Capturing)

----------------

Child

(Target)

----------------

Child
    ↑
Parent
    ↑
Grandparent

(Bubbling)
```

---

# Enabling Capturing

Default:

```javascript
element.addEventListener(
    "click",
    callback
);
```

Uses bubbling.

---

Capturing:

```javascript
element.addEventListener(
    "click",
    callback,
    true
);
```

---

Example

```javascript
parent.addEventListener(
    "click",
    ()=>{

        console.log("Parent");

    },
    true
);
```

---

HTML

```html
<div id="parent">

<button id="child">
Click
</button>

</div>
```

---

Output

```text
Parent
Child
```

Why?

Capturing travels:

```text
Parent

↓

Child
```

---

# Event Delegation

This concept is heavily used in:

* React
* Angular
* Vue
* Large Applications

---

Suppose:

```html
<ul>

<li>Item 1</li>
<li>Item 2</li>
<li>Item 3</li>
<li>Item 4</li>
<li>Item 5</li>

</ul>
```

Option 1:

```javascript
li1.addEventListener(...)
li2.addEventListener(...)
li3.addEventListener(...)
li4.addEventListener(...)
li5.addEventListener(...)
```

Bad.

---

Imagine:

```text
10,000 Elements
```

Creating:

```text
10,000 Listeners
```

Waste of memory.

---

# Better Solution

Attach one listener to parent.

HTML

```html
<ul id="list">

<li>Apple</li>
<li>Mango</li>
<li>Orange</li>

</ul>
```

---

JavaScript

```javascript
const list =
document.getElementById("list");

list.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.target.textContent
        );

    }
);
```

---

Output

Click Apple

```text
Apple
```

Click Mango

```text
Mango
```

Click Orange

```text
Orange
```

---

# How Does It Work?

Because of bubbling.

Flow:

```text
Click LI

↓

LI Event

↓

UL Event

↓

Listener Executes
```

---

Visual

```text
UL
 ↑
LI
```

Event reaches UL.

UL handles everything.

---

# Dynamic Elements Problem

Suppose:

```javascript
const button =
document.querySelector("button");

button.addEventListener(
    "click",
    ()=>{
        console.log("Clicked");
    }
);
```

Works only for existing button.

---

Now create button later.

```javascript
const newButton =
document.createElement("button");
```

Old listener doesn't affect it.

---

Event Delegation solves this.

Parent listens.

Future children automatically work.

---

# Todo Application Example

HTML

```html
<ul id="tasks">

<li>Learn HTML</li>
<li>Learn CSS</li>
<li>Learn JS</li>

</ul>
```

---

JavaScript

```javascript
const tasks =
document.getElementById("tasks");

tasks.addEventListener(
    "click",
    (e)=>{

        if(
            e.target.tagName === "LI"
        ){

            e.target.remove();

        }

    }
);
```

---

Output

Click:

```text
Learn HTML
```

Removed.

Click:

```text
Learn CSS
```

Removed.

No separate listeners needed.

---

# Most Important Mental Model

Whenever an event happens:

```text
Capturing
    ↓
Target
    ↓
Bubbling
```

Flow:

```text
Window
 ↓
Document
 ↓
HTML
 ↓
BODY
 ↓
Parent
 ↓
Child

(Target)

Child
 ↑
Parent
 ↑
BODY
 ↑
HTML
 ↑
Document
 ↑
Window
```

---

By the end of this part you understand:

✅ `removeEventListener()`

✅ Event Bubbling

✅ `stopPropagation()`

✅ Event Capturing

✅ Event Delegation

✅ Dynamic Elements Handling

✅ Why modern frameworks use delegation

In the next part we'll build real projects using event listeners:

* Counter App
* Color Changer
* Keyboard Tracker
* Live Character Counter
* Real-Time Form Validation
* Todo Application
* Image Gallery
* Accordion
* Modal Popup

and learn advanced listener options like:

```javascript
once
passive
capture
```

with complete hands-on code.
# JavaScript Event Listeners — Part 4 (Real Projects)

Now we know:

* addEventListener()
* Event Object
* Keyboard Events
* Input Events
* Submit Events
* Event Bubbling
* Event Capturing
* Event Delegation

Let's build real projects.

The goal is not memorizing syntax.

The goal is understanding:

```text
User Action
    ↓
Event Fires
    ↓
JavaScript Reacts
    ↓
DOM Updates
```

---

# Project 1: Counter Application

This is one of the best beginner projects.

---

## HTML

```html
<!DOCTYPE html>
<html>
<head>
    <title>Counter</title>
</head>
<body>

<h1 id="count">0</h1>

<button id="increase">
    Increase
</button>

<button id="decrease">
    Decrease
</button>

<button id="reset">
    Reset
</button>

<script src="script.js"></script>

</body>
</html>
```

---

## JavaScript

```javascript
let count = 0;

const countElement =
document.getElementById("count");

const increaseBtn =
document.getElementById("increase");

const decreaseBtn =
document.getElementById("decrease");

const resetBtn =
document.getElementById("reset");

increaseBtn.addEventListener(
    "click",
    () => {

        count++;

        countElement.textContent =
        count;

    }
);

decreaseBtn.addEventListener(
    "click",
    () => {

        count--;

        countElement.textContent =
        count;

    }
);

resetBtn.addEventListener(
    "click",
    () => {

        count = 0;

        countElement.textContent =
        count;

    }
);
```

---

## Flow

```text
User Clicks Increase
         ↓
click Event
         ↓
count++
         ↓
DOM Updated
         ↓
New Value Visible
```

---

# Project 2: Color Changer

---

## HTML

```html
<div id="box"></div>

<button id="changeColor">
    Change Color
</button>
```

---

## CSS

```css
#box{
    width:200px;
    height:200px;
    background:red;
}
```

---

## JavaScript

```javascript
const box =
document.getElementById("box");

const button =
document.getElementById("changeColor");

button.addEventListener(
    "click",
    () => {

        box.style.background =
        "blue";

    }
);
```

---

Before Click

```text
Red Box
```

After Click

```text
Blue Box
```

---

# Random Color Generator

More interesting.

---

```javascript
const colors = [
    "red",
    "green",
    "blue",
    "yellow",
    "purple"
];

button.addEventListener(
    "click",
    () => {

        const randomIndex =
        Math.floor(
            Math.random() *
            colors.length
        );

        box.style.background =
        colors[randomIndex];

    }
);
```

---

# Understanding

```javascript
Math.random()
```

Returns:

```text
0.23
0.88
0.56
0.11
```

Random values.

---

```javascript
Math.floor()
```

Removes decimals.

Example:

```javascript
Math.floor(3.9)
```

Output

```text
3
```

---

# Project 3: Keyboard Tracker

Shows which key user pressed.

---

## HTML

```html
<h1 id="output">
Press Any Key
</h1>
```

---

## JavaScript

```javascript
const output =
document.getElementById("output");

document.addEventListener(
    "keydown",
    (e) => {

        output.textContent =
        `You Pressed ${e.key}`;

    }
);
```

---

Example

Press:

```text
A
```

Output

```text
You Pressed A
```

---

Press:

```text
Enter
```

Output

```text
You Pressed Enter
```

---

# Project 4: Live Character Counter

Very useful.

Used in:

* Twitter
* Instagram
* Forms

---

## HTML

```html
<textarea
id="message">
</textarea>

<h3 id="count">
0 Characters
</h3>
```

---

## JavaScript

```javascript
const textarea =
document.getElementById("message");

const count =
document.getElementById("count");

textarea.addEventListener(
    "input",
    (e) => {

        count.textContent =
        `${e.target.value.length}
        Characters`;

    }
);
```

---

Example

User types:

```text
Hello
```

Output

```text
5 Characters
```

---

User types:

```text
JavaScript
```

Output

```text
10 Characters
```

---

# Understanding value.length

```javascript
"hello".length
```

Output

```text
5
```

---

```javascript
"javascript".length
```

Output

```text
10
```

---

# Project 5: Password Visibility Toggle

Common feature.

---

## HTML

```html
<input
type="password"
id="password">

<button id="toggle">
Show
</button>
```

---

## JavaScript

```javascript
const password =
document.getElementById("password");

const toggle =
document.getElementById("toggle");

toggle.addEventListener(
    "click",
    () => {

        if(
            password.type ===
            "password"
        ){

            password.type =
            "text";

        }
        else{

            password.type =
            "password";

        }

    }
);
```

---

## Understanding

Initially

```html
<input type="password">
```

Shows:

```text
******
```

---

After click

```html
<input type="text">
```

Shows:

```text
actual text
```

---

# Project 6: Live Search Filter

Very common interview-free real-world feature.

---

## HTML

```html
<input
type="text"
id="search">

<ul id="list">

<li>Apple</li>
<li>Mango</li>
<li>Orange</li>
<li>Banana</li>

</ul>
```

---

## JavaScript

```javascript
const search =
document.getElementById("search");

const items =
document.querySelectorAll("li");

search.addEventListener(
    "input",
    (e)=>{

        const value =
        e.target.value
        .toLowerCase();

        items.forEach(
            (item)=>{

                if(
                    item.textContent
                    .toLowerCase()
                    .includes(value)
                ){

                    item.style.display =
                    "block";

                }
                else{

                    item.style.display =
                    "none";

                }

            }
        );

    }
);
```

---

# Understanding

Suppose user types:

```text
ap
```

---

Check:

```javascript
"apple".includes("ap")
```

Output

```text
true
```

---

Check:

```javascript
"banana".includes("ap")
```

Output

```text
false
```

---

Result

```text
Apple Visible

Banana Hidden
```

---

# Project 7: Mouse Position Tracker

---

## HTML

```html
<h2 id="position">
Move Mouse
</h2>
```

---

## JavaScript

```javascript
const position =
document.getElementById("position");

document.addEventListener(
    "mousemove",
    (e)=>{

        position.textContent =
        `X:${e.clientX}
         Y:${e.clientY}`;

    }
);
```

---

Move Mouse:

```text
X:100 Y:250

X:120 Y:300

X:450 Y:100
```

Updates continuously.

---

# Understanding clientX and clientY

Suppose browser screen:

```text
-------------------
|
|
|
|
|
-------------------
```

Mouse Position:

```text
clientX
```

Horizontal position.

---

```text
clientY
```

Vertical position.

---

# Event Listener Mental Model

Every project follows:

```text
Select Element
      ↓
Add Listener
      ↓
Wait
      ↓
Event Occurs
      ↓
Read Event Data
      ↓
Update DOM
```

Example:

```javascript
button.addEventListener(
    "click",
    () => {

        heading.textContent =
        "Clicked";

    }
);
```

Flow:

```text
Button
   ↓
Click
   ↓
Listener
   ↓
Callback
   ↓
DOM Update
```

---




