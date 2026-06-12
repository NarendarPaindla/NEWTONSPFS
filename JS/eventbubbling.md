# Event Bubbling in JavaScript — Part 1 (Basics & Core Understanding)

Event Bubbling is one of the most important concepts in JavaScript.

Many developers use:

```javascript
button.addEventListener("click", () => {});
```

but don't understand:

```text
How the event travels?
Why parent listeners execute?
How Event Delegation works?
Why React uses Event Delegation?
```

Everything starts with Event Bubbling.

---

# What is Event Bubbling?

Event Bubbling means:

```text
Event starts at the target element

↓

Then moves upward through its ancestors

↓

Until it reaches document/window
```

The event "bubbles up" like an air bubble in water.

---

# Real Life Example

Imagine a child shouting.

```text
Child
  |
Parent
  |
Grandparent
```

Child says:

```text
Hello
```

Parent hears it.

Then:

```text
Grandparent hears it
```

Message travels upward.

Same happens with events.

---

# Understanding Parent and Child Elements

Consider:

```html
<div id="parent">

    <button id="child">
        Click Me
    </button>

</div>
```

DOM Structure:

```text
parent
   |
child
```

Here:

```text
DIV = Parent

BUTTON = Child
```

---

# First Bubbling Example

HTML

```html
<div id="parent">

    <button id="child">
        Click Me
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
        console.log("Parent Clicked");
    }
);

child.addEventListener(
    "click",
    () => {
        console.log("Child Clicked");
    }
);
```

---

# Question

When button is clicked:

```text
Click Me
```

What prints?

Many beginners think:

```text
Child Clicked
```

Only.

Wrong.

Output:

```text
Child Clicked

Parent Clicked
```

---

# Why?

Because:

```text
Button receives click

↓

Button listener executes

↓

Event bubbles upward

↓

Parent listener executes
```

Visual:

```text
Parent
   ↑
Child
```

Event Path:

```text
Child Click

↓

Child Listener

↓

Parent Listener
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

DOM Tree

```text
grandparent
      |
parent
      |
child
```

---

JavaScript

```javascript
const grandparent =
document.getElementById(
    "grandparent"
);

const parent =
document.getElementById(
    "parent"
);

const child =
document.getElementById(
    "child"
);

grandparent.addEventListener(
    "click",
    () => {
        console.log("Grandparent");
    }
);

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

# Output

When button clicked:

```text
Child

Parent

Grandparent
```

---

# Step-by-Step Flow

User clicks:

```text
Button
```

Step 1

```text
Child Listener Executes
```

Output

```text
Child
```

---

Step 2

Event moves upward.

```text
Parent Listener Executes
```

Output

```text
Parent
```

---

Step 3

Event moves upward.

```text
Grandparent Listener Executes
```

Output

```text
Grandparent
```

---

# Visual Animation

Imagine event as a ball.

```text
Grandparent
     ↑
Parent
     ↑
Child
```

User clicks child.

Ball starts here:

```text
Child
```

Moves:

```text
Child

↑

Parent

↑

Grandparent
```

This upward movement is:

```text
Event Bubbling
```

---

# Does Clicking Parent Trigger Child?

Let's test.

HTML

```html
<div id="parent">

    <button id="child">
        Child
    </button>

</div>
```

JavaScript

```javascript
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

Click Parent

Output

```text
Parent
```

Only.

---

Click Child

Output

```text
Child

Parent
```

---

Important Rule:

```text
Events Bubble Up

Events Do Not Bubble Down
```

---

# Understanding event.target

Event bubbling becomes easier when we inspect:

```javascript
event.target
```

HTML

```html
<div id="parent">

    <button id="child">
        Click
    </button>

</div>
```

JavaScript

```javascript
parent.addEventListener(
    "click",
    (e) => {

        console.log(e.target);

    }
);
```

---

User clicks button.

Output:

```html
<button id="child">
    Click
</button>
```

Not parent.

---

Why?

Because:

```text
target = Original Element

that started the event
```

---

Visual

```text
User Clicks Child

↓

target = Child

↓

Event Bubbles

↓

Parent Receives Event

↓

Target remains Child
```

---

# event.target vs this

Example

```javascript
parent.addEventListener(
    "click",
    function(e){

        console.log(e.target);

        console.log(this);

    }
);
```

Click button.

Output:

```text
e.target
↓

Button


this
↓

Parent
```

---

Difference:

```text
e.target
=
Original Element Clicked
```

---

```text
this
=
Element Handling Event
```

---

# Example

HTML

```html
<div id="box">

    <button>
        Click
    </button>

</div>
```

JavaScript

```javascript
box.addEventListener(
    "click",
    function(e){

        console.log(
            "Target:",
            e.target.tagName
        );

        console.log(
            "Current:",
            this.tagName
        );

    }
);
```

Click button.

Output

```text
Target: BUTTON

Current: DIV
```

---

# Why Event Bubbling Exists

Without bubbling:

```text
Need listener on every child
```

Imagine:

```html
<ul>

<li>Item 1</li>
<li>Item 2</li>
<li>Item 3</li>
<li>Item 4</li>
<li>Item 5</li>

</ul>
```

Without bubbling:

```javascript
li1.addEventListener(...)
li2.addEventListener(...)
li3.addEventListener(...)
li4.addEventListener(...)
li5.addEventListener(...)
```

Lots of code.

---

Because bubbling exists:

```javascript
ul.addEventListener(...)
```

One listener can manage all items.

This idea leads to:

```text
Event Delegation
```

which we'll study after fully mastering bubbling.

---

# Most Important Rules So Far

### Rule 1

Events move upward.

```text
Child

↑

Parent

↑

Grandparent
```

---

### Rule 2

Child click triggers parent listeners.

```text
Child Click

↓

Child Listener

↓

Parent Listener
```

---

### Rule 3

Parent click does not trigger child listener.

```text
Parent Click

↓

Parent Listener Only
```

---

### Rule 4

`event.target` always points to the original clicked element.

```javascript
e.target
```

---

### Rule 5

Bubbling is the foundation of Event Delegation.

```text
Event Bubbling
      ↓
Event Delegation
      ↓
Efficient Applications
```

---

# Event Bubbling in JavaScript — Part 2 (event.target, event.currentTarget, stopPropagation)

In Part 1 we learned:

```text
Child Click
     ↓
Parent
     ↓
Grandparent
```

This is Event Bubbling.

Now let's understand what actually happens inside the event object during bubbling.

---

# The Event Object During Bubbling

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

DOM Tree

```text
grandparent
      |
parent
      |
child
```

---

JavaScript

```javascript
grandparent.addEventListener(
    "click",
    (e)=>{
        console.log(e);
    }
);
```

When button is clicked:

```text
Browser creates event object
```

Inside that object:

```javascript
{
   target: child,
   currentTarget: grandparent,
   type: "click",
   ...
}
```

Many developers get confused between:

```javascript
e.target
```

and

```javascript
e.currentTarget
```

Let's master them.

---

# Understanding event.target

Rule:

```text
target =
Element That Started The Event
```

Original element clicked.

---

Example

HTML

```html
<div id="parent">

    <button id="child">
        Click
    </button>

</div>
```

JavaScript

```javascript
parent.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.target
        );

    }
);
```

Click button.

Output:

```html
<button id="child">
    Click
</button>
```

Not parent.

---

Why?

Because:

```text
Button started event
```

Therefore:

```javascript
e.target
```

always points to:

```text
Original Source
```

---

Visual

```text
Parent
   ↑
Child
```

User clicks:

```text
Child
```

Then:

```javascript
e.target
```

remains:

```text
Child
```

forever.

Even while event bubbles.

---

# Understanding event.currentTarget

Rule:

```text
currentTarget =
Element Currently Handling Event
```

---

Example

```javascript
parent.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.currentTarget
        );

    }
);
```

Click button.

Output:

```html
<div id="parent">
</div>
```

Because:

```text
Parent Listener
is executing
```

---

Visual

```text
Child Click
    ↓
Parent Listener Runs
```

During this moment:

```javascript
e.target
```

=

```text
Child
```

---

```javascript
e.currentTarget
```

=

```text
Parent
```

---

# Side-by-Side Comparison

HTML

```html
<div id="parent">

    <button id="child">
        Click
    </button>

</div>
```

JavaScript

```javascript
parent.addEventListener(
    "click",
    (e)=>{

        console.log(
            "Target:",
            e.target.tagName
        );

        console.log(
            "Current Target:",
            e.currentTarget.tagName
        );

    }
);
```

Click button.

Output

```text
Target: BUTTON

Current Target: DIV
```

---

# Why currentTarget Changes

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

JavaScript

```javascript
grandparent.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.currentTarget.id
        );

    }
);

parent.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.currentTarget.id
        );

    }
);

child.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.currentTarget.id
        );

    }
);
```

---

Click button.

Output

```text
child

parent

grandparent
```

Because listener changes.

---

But:

```javascript
e.target.id
```

Output

```text
child

child

child
```

always.

---

# Visual Representation

When button clicked:

```text
target
  ↓

child
```

Never changes.

---

currentTarget moves:

```text
child

↓

parent

↓

grandparent
```

---

# Easy Memory Trick

Imagine a cricket ball.

Player who hit ball:

```text
target
```

Never changes.

---

Player currently holding ball:

```text
currentTarget
```

Changes.

---

# Common Beginner Mistake

Suppose:

```javascript
parent.addEventListener(
    "click",
    (e)=>{

        console.log(
            e.target.id
        );

    }
);
```

Click child.

Output:

```text
child
```

Not:

```text
parent
```

Many beginners expect parent.

Wrong.

Because:

```text
target
=
who started event
```

---

# Bubbling Creates Problems Sometimes

Consider:

HTML

```html
<div id="parent">

<button id="child">
Click
</button>

</div>
```

JavaScript

```javascript
parent.addEventListener(
    "click",
    ()=>{

        console.log(
            "Parent Opened"
        );

    }
);

child.addEventListener(
    "click",
    ()=>{

        console.log(
            "Button Clicked"
        );

    }
);
```

---

Click button.

Output

```text
Button Clicked

Parent Opened
```

---

Maybe we wanted:

```text
Button Clicked
```

Only.

But bubbling triggered parent too.

---

# Solution: stopPropagation()

This method stops bubbling.

Syntax

```javascript
e.stopPropagation();
```

---

Example

```javascript
child.addEventListener(
    "click",
    (e)=>{

        e.stopPropagation();

        console.log(
            "Button Clicked"
        );

    }
);
```

---

Parent

```javascript
parent.addEventListener(
    "click",
    ()=>{

        console.log(
            "Parent Opened"
        );

    }
);
```

---

Output

```text
Button Clicked
```

Only.

---

# Understanding stopPropagation

Without:

```text
Child

↑

Parent

↑

Grandparent
```

Event keeps moving.

---

With:

```text
Child

❌ STOP
```

Event ends.

---

Visual

Without stopPropagation

```text
Click Child

↓

Child

↓

Parent

↓

Grandparent
```

---

With stopPropagation

```text
Click Child

↓

Child

↓

STOP
```

---

# Practical Example

HTML

```html
<div id="box">

<button id="btn">
Delete
</button>

</div>
```

JavaScript

```javascript
box.addEventListener(
    "click",
    ()=>{

        console.log(
            "Box Clicked"
        );

    }
);

btn.addEventListener(
    "click",
    (e)=>{

        e.stopPropagation();

        console.log(
            "Delete Clicked"
        );

    }
);
```

---

Output

Click Button

```text
Delete Clicked
```

Only.

---

Without stopPropagation

```text
Delete Clicked

Box Clicked
```

---

# Real World Example

Imagine:

```text
Modal Window
```

HTML

```html
<div class="overlay">

    <div class="modal">

        <button>
            Save
        </button>

    </div>

</div>
```

---

Overlay Listener

```javascript
overlay.addEventListener(
    "click",
    ()=>{
        closeModal();
    }
);
```

---

Button Listener

```javascript
button.addEventListener(
    "click",
    ()=>{
        saveData();
    }
);
```

---

Problem

Click Save button.

Output

```text
Save Data

Close Modal
```

Oops.

---

Solution

```javascript
button.addEventListener(
    "click",
    (e)=>{

        e.stopPropagation();

        saveData();

    }
);
```

Now:

```text
Save Data
```

Only.

---

# Event Path Visualization

Without stopPropagation

```text
Window
 ↑
Document
 ↑
HTML
 ↑
BODY
 ↑
Grandparent
 ↑
Parent
 ↑
Child
```

Click Child.

Event travels through all ancestors.

---

With stopPropagation

```text
Window
 ↑
Document
 ↑
HTML
 ↑
BODY
 ↑
Grandparent
 ↑
Parent

❌

Child
```

Stopped before reaching parent.

---

# Mental Model

Whenever you see:

```javascript
e.target
```

Think:

```text
Who Started Event?
```

---

Whenever you see:

```javascript
e.currentTarget
```

Think:

```text
Who Is Handling Event Right Now?
```

---

Whenever you see:

```javascript
e.stopPropagation()
```

Think:

```text
Do Not Bubble Further
```

---

Summary:

| Property              | Meaning                          |
| --------------------- | -------------------------------- |
| `e.target`            | Original clicked element         |
| `e.currentTarget`     | Element currently handling event |
| `e.stopPropagation()` | Stops bubbling                   |
| Bubbling              | Child → Parent → Grandparent     |

In the next part we'll dive into the **complete event flow**:

* Capturing Phase
* Target Phase
* Bubbling Phase
* `addEventListener(..., true)`
* Why events actually don't start with bubbling
* Visual diagrams of the full DOM event journey
* Understanding why Capturing exists and when to use it.
# Event Bubbling in JavaScript — Part 3 (Capturing Phase, Target Phase, Bubbling Phase)

Most developers think:

```text
User Clicks Button

↓

Bubbling Starts
```

This is only partially true.

In reality, every event goes through **3 phases**.

```text
1. Capturing Phase

↓

2. Target Phase

↓

3. Bubbling Phase
```

Understanding these phases will make Event Bubbling crystal clear.

---

# The Complete Event Journey

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

DOM Tree

```text
Window
  |
Document
  |
HTML
  |
BODY
  |
Grandparent
  |
Parent
  |
Child(Button)
```

When button is clicked, browser does NOT immediately start bubbling.

Instead it follows a complete route.

---

# Phase 1: Capturing Phase

Browser first travels from top to bottom.

```text
Window

↓

Document

↓

HTML

↓

BODY

↓

Grandparent

↓

Parent

↓

Child
```

This is called:

```text
Capturing Phase
```

or

```text
Event Capturing
```

Think of it like:

```text
Browser searching for
where the event happened
```

---

# Phase 2: Target Phase

Browser reaches:

```text
Child Button
```

Actual clicked element.

```text
Window
 ↓
Document
 ↓
HTML
 ↓
BODY
 ↓
Grandparent
 ↓
Parent
 ↓
Child
```

At this moment:

```text
Target Phase
```

begins.

The event has reached its destination.

---

# Phase 3: Bubbling Phase

Now event travels upward.

```text
Child

↑

Parent

↑

Grandparent

↑

BODY

↑

HTML

↑

Document

↑

Window
```

This is:

```text
Event Bubbling
```

---

# Visual Timeline

Suppose user clicks button.

Actual browser process:

```text
CAPTURING

Window
 ↓
Document
 ↓
HTML
 ↓
BODY
 ↓
Grandparent
 ↓
Parent
 ↓
Child

TARGET

Child

BUBBLING

Child
 ↑
Parent
 ↑
Grandparent
 ↑
BODY
 ↑
HTML
 ↑
Document
 ↑
Window
```

This is the complete event lifecycle.

---

# Why Don't We Usually See Capturing?

Because:

```javascript
addEventListener(
    "click",
    callback
);
```

defaults to:

```text
Bubbling Mode
```

Therefore listeners run during bubbling.

---

Example

```javascript
parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    }
);
```

Browser waits until bubbling phase reaches parent.

Then executes callback.

---

# Enabling Capturing

Syntax:

```javascript
addEventListener(
    event,
    callback,
    true
);
```

The third parameter:

```javascript
true
```

means:

```text
Execute During Capturing Phase
```

---

# First Capturing Example

HTML

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

    },
    true
);

child.addEventListener(
    "click",
    () => {

        console.log("Child");

    }
);
```

---

Question

What prints?

Many beginners expect:

```text
Child

Parent
```

Wrong.

Output:

```text
Parent

Child
```

---

Why?

Because:

```text
Parent Listener
is running during Capturing
```

Capturing happens before target phase.

---

Visual

```text
CAPTURING

Parent
   ↓
Child

TARGET

Child
```

Output:

```text
Parent

Child
```

---

# Multiple Capturing Listeners

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
    () => {
        console.log("Grandparent");
    },
    true
);

parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    },
    true
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

---

Output

```text
Grandparent

Parent

Child
```

---

Why?

Capturing travels downward.

```text
Grandparent

↓

Parent

↓

Child
```

---

# Capturing vs Bubbling

Let's compare.

---

## Bubbling Mode

```javascript
grandparent.addEventListener(
    "click",
    () => {
        console.log("Grandparent");
    }
);

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

Output

```text
Child

Parent

Grandparent
```

---

Flow

```text
Child

↑

Parent

↑

Grandparent
```

---

## Capturing Mode

```javascript
grandparent.addEventListener(
    "click",
    () => {
        console.log("Grandparent");
    },
    true
);

parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    },
    true
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

Output

```text
Grandparent

Parent

Child
```

---

Flow

```text
Grandparent

↓

Parent

↓

Child
```

---

# Mixed Example

This is where things get interesting.

HTML

```html
<div id="parent">

    <button id="child">
        Click
    </button>

</div>
```

JavaScript

```javascript
parent.addEventListener(
    "click",
    () => {

        console.log(
            "Parent Capture"
        );

    },
    true
);

parent.addEventListener(
    "click",
    () => {

        console.log(
            "Parent Bubble"
        );

    }
);

child.addEventListener(
    "click",
    () => {

        console.log(
            "Child"
        );

    }
);
```

---

Output

```text
Parent Capture

Child

Parent Bubble
```

---

Why?

Order:

```text
Capturing Phase

↓

Target Phase

↓

Bubbling Phase
```

Therefore:

```text
Parent Capture

↓

Child

↓

Parent Bubble
```

---

# Real Visualization

Imagine a parcel delivery.

Capturing:

```text
Head Office

↓

City Office

↓

Local Office

↓

House
```

---

Target:

```text
House Receives Parcel
```

---

Bubbling:

```text
House Sends Confirmation

↑

Local Office

↑

City Office

↑

Head Office
```

Exactly how events travel.

---

# Important Interview-Free Practical Fact

In real applications:

```text
95%+
```

of listeners use:

```javascript
addEventListener(
    "click",
    callback
);
```

which means:

```text
Bubbling
```

---

Capturing is used only in special situations:

* Advanced UI systems
* Framework internals
* Event monitoring
* Specific propagation control

---

# Mental Model

Whenever user clicks:

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

Remember:

```text
Going Down
=
Capturing

Clicked Element
=
Target

Going Up
=
Bubbling
```

---

At this point you fully understand:

✅ Capturing Phase

✅ Target Phase

✅ Bubbling Phase

✅ Why bubbling is not the whole story

✅ `addEventListener(..., true)`

✅ Capturing vs Bubbling

✅ Mixed Capturing and Bubbling execution order

Next we'll cover:

* `stopPropagation()` in Capturing vs Bubbling
* `stopImmediatePropagation()`
* Full Event Path (`composedPath()`)
* Nested Box Visual Project
* Debugging Event Flow
* Understanding exactly which listener runs first and why.

