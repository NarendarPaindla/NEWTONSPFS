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

Part 2: Responsive Design Deep Dive — Mobile First Thinking

Most beginners learn media queries first.

Professional developers learn how browsers calculate layouts first.

If you understand this, responsive design becomes easy.


---

How Browsers See Width

Suppose your mobile screen is:

375px

Browser width:

375px

Suppose your laptop:

1366px

Browser width:

1366px

Suppose your desktop:

1920px

Browser width:

1920px

The browser constantly knows:

window width

Responsive design means:

> Change layout according to available width.




---

Why Fixed Widths Are Dangerous

Bad:

.card{
    width:500px;
}

Mobile:

375px screen

Card:

500px

Result:

Horizontal Scroll


---

Good:

.card{
    width:100%;
}

Mobile:

375px

Card:

375px

Desktop:

1200px container

Card:

1200px

Automatically adapts.


---

Understanding Relative Units

Most beginners use:

width:300px;
font-size:20px;

Professionals prefer:

%
rem
em
vw
vh

Let's understand all.


---

Percentage (%)

Example:

.box{
    width:50%;
}

Means:

Take 50% of parent width

Parent:

1000px

Box:

500px


---

Parent:

400px

Box:

200px

Automatically responsive.


---

rem Unit

Most important unit.

Example:

font-size:1rem;

Default browser font:

16px

Therefore:

1rem = 16px


---

Examples

1rem = 16px
2rem = 32px
3rem = 48px


---

Instead of:

font-size:32px;

Use:

font-size:2rem;

Why?

Because rem scales better across devices.


---

em Unit

Depends on parent.

Example:

.parent{
    font-size:20px;
}

.child{
    font-size:2em;
}

Child becomes:

40px

Because:

2 × 20


---

vw (Viewport Width)

Very useful.

width:50vw;

Means:

50% of screen width

Screen:

1000px

Width:

500px


---

Screen:

400px

Width:

200px

Automatically changes.


---

vh (Viewport Height)

Example:

height:100vh;

Means:

Take full screen height

Laptop:

800px height

Section:

800px

Mobile:

700px height

Section:

700px


---

Build Hero Section

Add below navbar.

HTML

<section class="hero">

    <div class="container">

        <div class="hero-content">

            <h1>
                Explore The World
            </h1>

            <p>
                Discover beautiful destinations and unforgettable experiences.
            </p>

            <button>
                Book Now
            </button>

        </div>

    </div>

</section>


---

Hero CSS

.hero{

    height:100vh;

    display:flex;

    justify-content:center;

    align-items:center;

    text-align:center;
}


---

Let's Understand

height:100vh

Suppose screen height:

800px

Hero becomes:

800px

Full screen.


---

justify-content:center

Main axis:

Vertical

because flex direction is column later.

Content moves to center.


---

align-items:center

Horizontal centering.

Result:

Explore World

          Text

        Button

Perfect center.


---

Style Hero Content

.hero-content h1{
    font-size:4rem;
}

.hero-content p{
    font-size:1.2rem;

    margin-top:20px;

    margin-bottom:20px;
}


---

Desktop Output

Explore The World

Discover beautiful destinations...

[ Book Now ]

Looks great.


---

Problem On Mobile

Desktop:

4rem

64px

Good.


---

Mobile:

64px

Too big.

Text wraps badly.


---

Responsive Design Means Fixing This


---

Media Queries

Most important responsive feature.

Syntax:

@media(condition){

}

Example:

@media(max-width:768px){

}

Meaning:

Apply styles
ONLY IF

screen <= 768px


---

Visual

Desktop
1200px

No

Tablet
768px

Yes

Mobile
375px

Yes


---

First Responsive Rule

@media(max-width:768px){

    .hero-content h1{
        font-size:2.5rem;
    }

}


---

Desktop

64px

Mobile

40px

Much better.


---

Responsive Navbar

Current navbar:

TravelX
Home
About
Tours
Contact
Login

Won't fit on mobile.


---

Temporary Solution

@media(max-width:768px){

    .menu{
        display:none;
    }

}


---

Desktop

TravelX Home About Tours Contact Login

Mobile

TravelX Login

Now fits.


---

Professional Breakpoints

Most companies use something like:

320px
480px
768px
1024px
1200px
1440px


---

Meaning

Mobile

0 - 767px

Tablet

768 - 1023px

Laptop

1024 - 1439px

Desktop

1440+


---

Mobile First Design

Modern approach:

Instead of:

Desktop first

Professionals write:

Mobile first


---

Example

Default:

.card{
    width:100%;
}

Mobile style first.

Then:

@media(min-width:768px){

.card{
    width:50%;
}

}

Tablet.

Then:

@media(min-width:1024px){

.card{
    width:33.33%;
}

}

Desktop.


---

Why Companies Prefer Mobile First

Because:

80%+ traffic

often comes from mobile devices.


---

Next Section: Destinations Cards

We'll create 6 travel destination cards.

Desktop:

Card Card Card

Card Card Card

Tablet:

Card Card

Card Card

Card Card

Mobile:

Card

Card

Card

Card

While building it, you'll learn:

✅ CSS Grid

✅ repeat()

✅ fr unit

✅ auto-fit

✅ auto-fill

✅ minmax()

✅ Real-world responsive card systems

These are heavily asked in frontend interviews and used daily in React projects.

Part 3: Building a Fully Responsive Destinations Section Using CSS Grid

Now we are entering one of the most important topics in modern CSS.

Many beginners try to build card layouts using:

float
inline-block
flexbox

But professionals usually use:

CSS Grid

for 2D layouts.


---

What is CSS Grid?

Think about a table.

Example:

+-------+-------+-------+
| Card1 | Card2 | Card3 |
+-------+-------+-------+
| Card4 | Card5 | Card6 |
+-------+-------+-------+

Grid allows us to create layouts like this very easily.


---

Add Destination Section

Below Hero Section.

HTML

<section class="destinations">

    <div class="container">

        <div class="section-title">
            <h2>Popular Destinations</h2>
        </div>

        <div class="destination-grid">

            <div class="card">
                <img src="https://picsum.photos/400/250?1">
                <h3>Paris</h3>
                <p>City of Lights</p>
            </div>

            <div class="card">
                <img src="https://picsum.photos/400/250?2">
                <h3>Dubai</h3>
                <p>Luxury Destination</p>
            </div>

            <div class="card">
                <img src="https://picsum.photos/400/250?3">
                <h3>Maldives</h3>
                <p>Tropical Paradise</p>
            </div>

            <div class="card">
                <img src="https://picsum.photos/400/250?4">
                <h3>Switzerland</h3>
                <p>Mountain Beauty</p>
            </div>

            <div class="card">
                <img src="https://picsum.photos/400/250?5">
                <h3>Tokyo</h3>
                <p>Modern Culture</p>
            </div>

            <div class="card">
                <img src="https://picsum.photos/400/250?6">
                <h3>Singapore</h3>
                <p>Urban Excellence</p>
            </div>

        </div>

    </div>

</section>


---

Section Styling

.destinations{
    padding:80px 20px;
}


---

Why Padding?

Without:

Hero
Destinations

Everything touches each other.

Bad design.


---

With:

padding:80px 20px;

Browser calculates:

80px top
80px bottom
20px left
20px right

Much better spacing.


---

Section Title

.section-title{
    text-align:center;
    margin-bottom:50px;
}

.section-title h2{
    font-size:3rem;
}


---

Output

Popular Destinations

centered nicely.


---

Create Grid

.destination-grid{

    display:grid;

    grid-template-columns:
    repeat(3,1fr);

    gap:30px;
}


---

Understanding Grid Deeply

display:grid

Tells browser:

I want a grid layout.


---

Without:

Card1

Card2

Card3

Card4

All vertical.


---

With:

display:grid;

Grid system activates.


---

Understanding repeat()

repeat(3,1fr)

means:

1fr 1fr 1fr


---

Equivalent:

grid-template-columns:
1fr
1fr
1fr;


---

What is fr?

Very important interview question.

fr means:

Fraction of available space


---

Example

Container:

1200px

Grid:

grid-template-columns:
1fr 1fr 1fr;

Browser says:

1200 ÷ 3

400px
400px
400px


---

Result

Card1 Card2 Card3


---

Another Example

grid-template-columns:
1fr 2fr 1fr;

Available:

1200px

Total fractions:

1+2+1 = 4

Each fraction:

1200 ÷ 4

300px

Result:

300px
600px
300px


---

Visual

+----+----------+----+
|300 |   600    |300 |
+----+----------+----+


---

Gap Property

gap:30px;

Old way:

margin-right
margin-bottom

Modern way:

gap

Works beautifully.


---

Visual

Without gap:

CardCardCard

With gap:

Card   Card   Card


---

Style Cards

.card{

    background:white;

    border-radius:15px;

    overflow:hidden;

    box-shadow:
    0 5px 20px rgba(0,0,0,.1);

    transition:.3s;
}


---

Why border-radius?

Without:

+---------+
|         |
+---------+


---

With:

border-radius:15px;

╭─────────╮
│         │
╰─────────╯

Modern look.


---

Why overflow:hidden?

Suppose image is larger.

Without:

Image spills outside card

With:

overflow:hidden;

Image stays inside.


---

Box Shadow

box-shadow:
0 5px 20px rgba(0,0,0,.1);

Breakdown:

horizontal = 0
vertical   = 5px
blur       = 20px
color      = rgba(...)


---

Visual

Without:

Flat card

With:

Elevated card


---

Image Styling

.card img{

    width:100%;

    display:block;
}


---

Why width:100%?

Image automatically fills card.


---

Without:

Image width may overflow.


---

With:

width:100%;

Perfect fit.


---

Content Styling

.card h3{
    padding:15px;
}

.card p{
    padding:0 15px 20px;
}


---

Hover Effect

.card:hover{

    transform:
    translateY(-10px);
}


---

Normal

Card

Hover

Card

Moves upward.

Modern UI effect.


---

Current Desktop Layout

+------+ +------+ +------+
|Card1 | |Card2 | |Card3 |
+------+ +------+ +------+

+------+ +------+ +------+
|Card4 | |Card5 | |Card6 |
+------+ +------+ +------+

Looks good.


---

Problem on Mobile

Mobile width:

375px

Still trying:

repeat(3,1fr)

Result:

Tiny tiny tiny cards

Bad UX.


---

First Responsive Fix

@media(max-width:768px){

.destination-grid{

grid-template-columns:
1fr;

}

}


---

Mobile Output

Card1

Card2

Card3

Card4

Card5

Card6

Works.


---

Tablet Fix

@media(max-width:1024px){

.destination-grid{

grid-template-columns:
1fr 1fr;

}

}


---

Tablet Output

Card1 Card2

Card3 Card4

Card5 Card6

Perfect.


---

Industry-Level Solution

Professionals avoid writing many media queries.

Instead:

.destination-grid{

display:grid;

grid-template-columns:
repeat(
auto-fit,
minmax(280px,1fr)
);

gap:30px;
}

This is one of the most powerful CSS Grid techniques.


---

Understanding minmax()

minmax(280px,1fr)

Means:

Minimum = 280px

Maximum = 1fr

Browser says:

> Never make a card smaller than 280px.




---

Suppose screen:

1200px

Can fit 4 cards.

Result:

4 columns


---

Screen:

800px

Can fit 2 cards.

Result:

2 columns


---

Screen:

400px

Can fit 1 card.

Result:

1 column

Automatically.

No media queries needed.


---

Understanding auto-fit

auto-fit

Browser tries:

Fit as many columns
as possible.


---

Example

Container:

1000px

Card minimum:

280px

Browser calculates:

1000 ÷ 280

≈ 3 cards

Creates:

3 columns

Automatically.


---

Professional Grid Formula

You'll see this everywhere:

grid-template-columns:
repeat(
auto-fit,
minmax(250px,1fr)
);

or

grid-template-columns:
repeat(
auto-fit,
minmax(280px,1fr)
);

or

grid-template-columns:
repeat(
auto-fit,
minmax(300px,1fr)
);

This single line can replace many media queries.


---

What We Learned

✅ CSS Grid

✅ display:grid

✅ grid-template-columns

✅ repeat()

✅ fr unit

✅ gap

✅ Responsive cards

✅ Media Queries

✅ auto-fit

✅ minmax()

✅ Modern industry layout technique


---

In the next part we'll build the Services Section and go much deeper into:

Flexbox Mastery

flex-grow

flex-shrink

flex-basis

flex-wrap

responsive row-to-column conversion

real-world service cards

common interview questions on Flexbox vs Grid


This is where most frontend interviews focus heavily.

Part 4: Flexbox Mastery Through a Professional Services Section

So far:

✅ Navbar → Flexbox

✅ Hero → Responsive Typography

✅ Destinations → CSS Grid

Now we will master Flexbox.


---

First Understand

A very common interview question:

When should we use Flexbox?

Use Flexbox when:

One-dimensional Layout

Example:

Logo   Menu   Button

One row.

or

Card
Card
Card

One column.


---

When should we use Grid?

Use Grid when:

Two-dimensional Layout

Example:

Card Card Card

Card Card Card

Rows + Columns.


---

Easy Rule

Use:

Flexbox → Row OR Column

Use:

Grid → Row AND Column


---

Build Services Section

Add below Destinations.

HTML

<section class="services">

    <div class="container">

        <div class="section-title">
            <h2>Our Services</h2>
        </div>

        <div class="service-wrapper">

            <div class="service-card">

                <div class="icon">
                    ✈️
                </div>

                <h3>Flight Booking</h3>

                <p>
                    Get affordable flight tickets worldwide.
                </p>

            </div>

            <div class="service-card">

                <div class="icon">
                    🏨
                </div>

                <h3>Hotel Reservation</h3>

                <p>
                    Book luxury and budget hotels.
                </p>

            </div>

            <div class="service-card">

                <div class="icon">
                    🚗
                </div>

                <h3>Transport</h3>

                <p>
                    Convenient transportation services.
                </p>

            </div>

        </div>

    </div>

</section>


---

Service Section Styling

.services{
    padding:80px 20px;
}

Same spacing approach.


---

Create Flex Layout

.service-wrapper{

    display:flex;

    gap:30px;
}


---

Without Flexbox

Flight

Hotel

Transport

Vertical.


---

With Flexbox

Flight   Hotel   Transport

Horizontal.


---

Style Service Cards

.service-card{

    background:white;

    padding:30px;

    text-align:center;

    border-radius:15px;

    box-shadow:
    0 5px 20px rgba(0,0,0,.1);
}


---

Output

+-----------+
|     ✈️    |
| Flight    |
| Text      |
+-----------+


---

Problem

Currently:

Flight Hotel Transport

Looks okay.

But cards have different widths.


---

Equal Width Cards

Add:

.service-card{
    flex:1;
}


---

What is flex:1?

Interview favorite.


---

Browser sees:

flex:1;

Equivalent to:

flex-grow:1;
flex-shrink:1;
flex-basis:0;


---

Understanding flex-grow

Example:

Container:

1200px

Three cards:

flex-grow:1;

Browser divides equally.

400
400
400


---

Visual

+------+ +------+ +------+
|Card1 | |Card2 | |Card3 |
+------+ +------+ +------+


---

Different Grow Values

Example:

.card1{
    flex-grow:1;
}

.card2{
    flex-grow:2;
}

.card3{
    flex-grow:1;
}


---

Total:

1+2+1 = 4

Available:

1200px

Each fraction:

300px

Result:

300
600
300


---

Visual

+----+ +--------+ +----+
|300 | |  600   | |300 |
+----+ +--------+ +----+


---

Understanding flex-basis

Think:

Starting Width

Example:

.service-card{
    flex-basis:300px;
}

Browser starts card width at:

300px

Then distributes remaining space.


---

Understanding flex-shrink

Example:

Container:

600px

Cards:

width:300px;

Total needed:

900px

Container:

600px

Not enough.


---

Browser shrinks items.

That's:

flex-shrink:1;

(default)


---

Prevent Shrinking

flex-shrink:0;

Now cards refuse to shrink.

Result:

Horizontal Scroll

Sometimes useful.

Usually avoid.


---

The Biggest Flexbox Property

flex-wrap

Current:

.service-wrapper{

display:flex;
}


---

Desktop

Card Card Card

Good.


---

Mobile

CardCardCard

Bad.


---

Add:

.service-wrapper{

display:flex;

flex-wrap:wrap;
}


---

Meaning:

If space unavailable

Move next item
to next line


---

Visual

Desktop

Card Card Card

Tablet

Card Card

Card


---

Mobile

Card

Card

Card

Automatically.


---

Better Responsive Solution

.service-card{

    flex:1 1 300px;
}


---

Interview Question:

Explain

flex:1 1 300px;

Answer:

flex-grow:1;
flex-shrink:1;
flex-basis:300px;


---

Meaning:

Start at 300px.

Grow if extra space.

Shrink if needed.


---

This is heavily used in industry.


---

Add Hover Animation

.service-card{

transition:.3s;
}

.service-card:hover{

transform:
translateY(-10px);
}


---

Normal

Card

Hover

Card

Moves upward.

Professional effect.


---

Understanding Flex Direction

Default:

flex-direction:row;

Visual

A B C


---

Column:

flex-direction:column;

Visual

A

B

C


---

Example

.service-card{

display:flex;

flex-direction:column;

align-items:center;
}

Now content inside card becomes:

Icon

Heading

Text

Perfectly aligned.


---

Understanding Main Axis

Very important.

Suppose:

display:flex;

flex-direction:row;

Main axis:

Horizontal

Cross axis:

Vertical


---

Visual

-------------------->
Main Axis

|
|
|
v

Cross Axis


---

justify-content Works On Main Axis

Example:

justify-content:center;

Row direction:

A B C

Centered horizontally.


---

align-items Works On Cross Axis

align-items:center;

A
B
C

Centered vertically.


---

Most Common Interview Confusion

Many students memorize:

justify-content
align-items

Wrong approach.


---

Remember:

justify-content

controls MAIN axis

align-items

controls CROSS axis


---

Then ask:

What is main axis?

Depends on:

flex-direction


---

If:

flex-direction:row;

Main axis:

Horizontal


---

If:

flex-direction:column;

Main axis:

Vertical


---

Real Industry Responsive Pattern

Most websites use:

Desktop

Card Card Card

Tablet

Card Card

Card

Mobile

Card

Card

Card

Achieved simply using:

.service-wrapper{

display:flex;

flex-wrap:wrap;

gap:30px;
}

.service-card{

flex:1 1 300px;
}

No media query required.

Very powerful.


---

Current Website Structure

NAVBAR

HERO

DESTINATIONS
(Grid)

SERVICES
(Flexbox)

Next, we will build the Gallery Section, where you'll learn:

Advanced CSS Grid

grid-column

grid-row

Masonry-style layouts

object-fit

aspect-ratio

responsive image galleries

image optimization concepts

how Pinterest-like layouts work


This is where CSS Grid becomes truly powerful.

