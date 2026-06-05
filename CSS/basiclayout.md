# CSS MASTERCLASS – TOPIC 5

# BASIC LAYOUT

## Understanding How Elements Live on a Web Page

---

# Before Learning Layout

Ask students:

### Why does this happen?

```html
<h1>Welcome</h1>

<p>Hello</p>

<button>Click</button>

<button>Login</button>
```

Output:

```text
Welcome

Hello

[Click] [Login]
```

Question:

Why did:

```text
h1
```

go to a new line?

Why did:

```text
p
```

go to a new line?

Why did:

```text
button
```

stay on the same line?

---

Most beginners don't know.

The answer is:

# DISPLAY BEHAVIOR

Every HTML element has a default display type.

---

# MEMORY RULE

Remember:

```text
HTML Elements

=
People

Every person has behavior.

Every HTML element has display behavior.
```

---

# THE MOST IMPORTANT PROPERTY

```css
display
```

This property controls:

```text
How an element behaves

How much space it takes

Whether it starts a new line

Whether width works

Whether height works
```

---

# DISPLAY TYPES

We will master:

```text
Block

Inline

Inline-Block

None
```

These four explain 80% of layout behavior.

---

# BLOCK ELEMENTS

---

## Definition

A block element:

```text
Starts on a new line

Takes full available width
```

---

# Example

```html
<h1>Heading</h1>

<p>Paragraph</p>

<div>Box</div>
```

All are block elements.

---

Visual

```text
┌────────────────────┐
│ Heading            │
└────────────────────┘

┌────────────────────┐
│ Paragraph          │
└────────────────────┘

┌────────────────────┐
│ Box                │
└────────────────────┘
```

---

# Memory Trick

Block Element

=

A person who wants an entire bench.

Nobody sits beside them.

---

# Common Block Elements

```html
<div>

<p>

<h1>

<h2>

<h3>

<section>

<header>

footer

article

main

nav
```

---

# Activity

```html
<div>HTML</div>

<div>CSS</div>

<div>JavaScript</div>
```

Observe:

Each appears on a separate line.

---

# Width Behavior

Block elements automatically take:

```text
100% available width
```

---

Example

```html
<div>Hello</div>
```

```css
div
{
 background:yellow;
}
```

Notice:

Yellow stretches across screen.

---

Why?

Because block elements take full width.

---

# Width Control

```css
div
{
 width:300px;
}
```

Now width becomes controlled.

---

# Activity

Experiment:

```css
width:100px;
```

```css
width:300px;
```

```css
width:500px;
```

Observe.

---

# INLINE ELEMENTS

Now comes the opposite behavior.

---

# Definition

Inline elements:

```text
Do NOT start a new line

Take only required width
```

---

Example

```html
<span>HTML</span>

<span>CSS</span>

<span>JavaScript</span>
```

Output

```text
HTML CSS JavaScript
```

Same line.

---

# Visual

```text
┌────┐┌────┐┌──────────┐
│HTML││CSS ││JavaScript│
└────┘└────┘└──────────┘
```

---

# Memory Trick

Inline Element

=

Students sitting side by side on a bench.

---

# Common Inline Elements

```html
<span>

<a>

strong

em

label

small
```

---

# Activity

```html
<span>One</span>

<span>Two</span>

<span>Three</span>
```

Observe.

---

# Biggest Inline Limitation

Students must understand this deeply.

---

Example

```html
<span>
Hello CSS
</span>
```

```css
span
{
 width:300px;

 height:200px;
}
```

Nothing happens.

---

Students get confused.

---

# Why?

Because:

```text
Width doesn't work properly

Height doesn't work properly

on inline elements
```

---

# Memory Trick

Inline elements are lightweight.

They don't want large boxes.

---

# Demonstration

```html
<span>
HTML
</span>
```

```css
span
{
 background:yellow;

 width:500px;
}
```

Observe.

Width ignored.

---

# BLOCK VS INLINE

| Feature      | Block | Inline |
| ------------ | ----- | ------ |
| New Line     | Yes   | No     |
| Full Width   | Yes   | No     |
| Width Works  | Yes   | No     |
| Height Works | Yes   | No     |

---

# THE SOLUTION

# INLINE-BLOCK

Professional developers often need:

```text
Stay on same line

AND

Allow width/height
```

---

This is where:

```css
display:inline-block;
```

enters.

---

# Example

```html
<div class="box">
HTML
</div>

<div class="box">
CSS
</div>

<div class="box">
JS
</div>
```

---

CSS

```css
.box
{
 display:inline-block;

 width:150px;

 height:150px;

 background:skyblue;
}
```

---

Output

```text
┌─────┐ ┌─────┐ ┌─────┐
│HTML │ │CSS  │ │ JS  │
└─────┘ └─────┘ └─────┘
```

---

# What Happened?

Same line.

AND

Width works.

AND

Height works.

Best of both worlds.

---

# Memory Trick

Inline-Block

=

Students sitting together

but each has their own chair.

---

# Activity

Create 4 colored boxes.

Use:

```css
display:inline-block;
```

Observe alignment.

---

# DISPLAY NONE

Another powerful property.

---

# What Does It Do?

Completely removes element.

---

HTML

```html
<h1>Hello</h1>
```

CSS

```css
h1
{
 display:none;
}
```

Output

Nothing.

---

Element disappears.

---

# Memory Trick

Display None

=

Element goes on vacation.

Not visible.

Not occupying space.

---

# Visual

Before

```text
Heading

Paragraph
```

After

```text
Paragraph
```

Heading removed completely.

---

# Real Website Examples

Navigation Menus

Mobile Menus

Dropdown Menus

Popups

Tabs

All use:

```css
display:none;
```

---

# ACTIVITY

```html
<h1>HTML</h1>

<h1>CSS</h1>

<h1>JavaScript</h1>
```

Hide second heading.

```css
h1:nth-child(2)
{
 display:none;
}
```

Observe.

---

# VISIBILITY PROPERTY

Students often confuse:

```css
display:none;
```

and

```css
visibility:hidden;
```

---

# visibility:hidden

Element becomes invisible.

BUT

Space remains.

---

Example

```css
h1
{
 visibility:hidden;
}
```

---

Visual

```text
[empty space]

Paragraph
```

Space still exists.

---

# display:none

Visual

```text
Paragraph
```

No space.

---

# Memory Trick

Visibility Hidden

=

Invisible Man standing there.

You can't see him.

Space still occupied.

---

Display None

=

Person left the room.

No space occupied.

---

# Activity

Create 2 boxes.

Apply:

```css
visibility:hidden;
```

Observe.

Then:

```css
display:none;
```

Observe.

Students instantly understand.

---

# FIRST REAL PROJECT

# Navigation Bar Without Flexbox

---

## HTML

```html
<nav>

<a href="">Home</a>

<a href="">About</a>

<a href="">Services</a>

<a href="">Contact</a>

</nav>
```

---

## CSS

```css
nav
{
 background:#2563EB;

 padding:20px;
}

a
{
 color:white;

 text-decoration:none;

 display:inline-block;

 margin-right:20px;
}
```

---

# What Students Learn

Links are:

```html
<a>
```

which are inline by default.

---

Using:

```css
display:inline-block;
```

allows spacing and sizing control.

---

# SECOND PROJECT

# Service Cards

---

## HTML

```html
<div class="card">
HTML
</div>

<div class="card">
CSS
</div>

<div class="card">
JavaScript
</div>
```

---

## CSS

```css
.card
{
 display:inline-block;

 width:200px;

 height:150px;

 background:lightblue;

 margin:10px;

 padding:20px;
}
```

---

Output

```text
┌───────┐ ┌───────┐ ┌───────┐
│ HTML  │ │ CSS   │ │ JS    │
└───────┘ └───────┘ └───────┘
```

---

# MEMORY MAP

```text
display:block

=
New Line
Full Width

display:inline

=
Same Line
No Width/Height

display:inline-block

=
Same Line
Width/Height Works

display:none

=
Removed Completely
```

---

# ULTIMATE MEMORY STORY

Imagine a Classroom.

```text
Block
=
Student wants entire bench

Inline
=
Students share bench

Inline-Block
=
Students share row
but have individual chairs

Display None
=
Student absent

Visibility Hidden
=
Student present
but invisible
```

Students rarely forget display behavior after this story.

---




These concepts are where websites start behaving like real applications rather than static pages.
