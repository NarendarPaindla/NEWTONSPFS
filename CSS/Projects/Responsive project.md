Perfect. Since you want to master Responsive Design deeply, we will not learn it through small examples.

We will build a Professional Travel Website Landing Page from scratch and make it responsive for:

1. Mobile (320px - 767px)


2. Tablet (768px - 1023px)


3. Laptop (1024px - 1439px)


4. Desktop (1440px+)



While building, I'll explain:

WHY we use a property

WHAT problem it solves

HOW browsers calculate it

WHAT happens if we don't use it

Common interview questions

Real industry practices



---

Project We Will Build

--------------------------------------------------
LOGO      HOME ABOUT TOURS CONTACT    LOGIN
--------------------------------------------------

              HERO SECTION
      Explore The World With Us

          [Book Now Button]

--------------------------------------------------

DESTINATIONS SECTION

Card    Card    Card

--------------------------------------------------

SERVICES SECTION

Service Service Service

--------------------------------------------------

GALLERY

Images Grid

--------------------------------------------------

FOOTER
--------------------------------------------------

By the end you will understand:

✅ Responsive Design

✅ Flexbox

✅ CSS Grid

✅ Media Queries

✅ Relative Units

✅ Fluid Layouts

✅ Mobile First Design

✅ Industry Standards


---

Step 1

What is Responsive Design?

Responsive Design means:

> A website automatically adapts to different screen sizes.



Example:

Desktop:

--------------------------------
| Card | Card | Card | Card |
--------------------------------

Mobile:

-----------
| Card |
-----------
| Card |
-----------
| Card |
-----------

Same website.

Different layouts.


---

Why Responsive Design Exists

Before smartphones:

Only Desktop

Now:

Desktop
Laptop
Tablet
Mobile
TV
Foldable Phones

One fixed width website no longer works.


---

Bad Example

.container{
    width:1200px;
}

Problem:

Mobile screen:

375px

Website:

1200px

Result:

❌ Horizontal Scroll


---

Good Example

.container{
    width:100%;
}

Now browser says:

Take full available width.

Desktop:

1920px

Container:

1920px

Mobile:

375px

Container:

375px

Automatically responsive.


---

Create Project Structure

index.html

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>Travel Website</title>

    <link rel="stylesheet" href="style.css">
</head>

<body>

</body>

</html>


---

Most Important Responsive Tag

<meta name="viewport"
      content="width=device-width, initial-scale=1.0">

Interview Question:

What happens if viewport tag is removed?

Browser thinks:

Website designed for desktop

Mobile shrinks entire website.

Everything becomes tiny.

Therefore ALWAYS use viewport.


---

Step 2

CSS Reset

style.css

*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}


---

Why box-sizing?

Without:

width:300px;
padding:20px;

Actual width:

340px

Because:

300
+20 left
+20 right
------
340

With:

box-sizing:border-box;

Actual width:

300px

Much easier.

Industry standard.


---

Step 3

Create Container

HTML

<body>

    <div class="container">

    </div>

</body>

CSS

.container{
    width:100%;
    max-width:1200px;
    margin:auto;
}


---

Understanding Every Property

width:100%

Means:

Take available width.

Desktop:

1200px+

Mobile:

375px


---

max-width:1200px

Without it:

Large monitor:

3000px wide

Content stretches too much.

Bad UX.


---

With:

max-width:1200px;

Content never exceeds:

1200px

Professional websites use:

1140px
1200px
1280px
1400px


---

margin:auto

Centers container.

Example:

Monitor Width

1920px

Container:

1200px

Remaining:

720px

Browser:

360px left
360px right

Perfect center.


---

Let's Build Navbar

HTML

<header>

    <div class="container">

        <nav class="navbar">

            <div class="logo">
                TravelX
            </div>

            <ul class="menu">

                <li><a href="#">Home</a></li>

                <li><a href="#">About</a></li>

                <li><a href="#">Tours</a></li>

                <li><a href="#">Contact</a></li>

            </ul>

            <button class="btn">
                Login
            </button>

        </nav>

    </div>

</header>


---

CSS

.navbar{

    display:flex;

    justify-content:space-between;

    align-items:center;

    padding:20px;
}


---

Let's Understand Deeply

Without Flexbox:

Logo

Menu

Button

Everything goes vertically.

Because:

div = block element


---

With:

display:flex;

Direction:

row

Default.

Result:

Logo Menu Button

Same row.


---

justify-content

justify-content:space-between;

Before:

Logo Menu Button

After:

Logo          Menu          Button

Browser calculates:

Remaining space

and distributes it.


---

align-items:center

Cross-axis alignment.

Before:

Logo
       Menu
 Button

After:

Logo  Menu  Button

All vertically centered.


---

Style Menu

.menu{
    list-style:none;

    display:flex;

    gap:30px;
}


---

Why gap?

Old way:

margin-right:30px;

Modern way:

gap:30px;

Cleaner.


---

Style Links

.menu a{
    text-decoration:none;
    color:black;
    font-size:18px;
}


---

Button

.btn{
    padding:12px 25px;
    border:none;
    background:black;
    color:white;
    cursor:pointer;
}


---

Current Desktop Layout

TravelX

Home About Tours Contact

Login

All in one row.

Looks good.


---

Now Think Like a Responsive Developer

Question:

What happens on mobile?

Screen:

375px

Navbar:

TravelX Home About Tours Contact Login

Can it fit?

NO.

Everything breaks.

This is where responsive design starts.


---

In the next part we will build:

Mobile Responsive Navbar

and learn:

Media Queries

Breakpoints

Mobile First Design

Hamburger Menu Layout

Relative Units (rem, em, %, vw, vh)

Why px is dangerous in responsive design

Industry-level responsive workflow


This is where real responsive design begins.
