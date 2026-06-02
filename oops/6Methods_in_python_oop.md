# OOPs Concept 6: Methods in Python OOP

Until now, we learned:

✅ Class

✅ Object

✅ Constructor

✅ self

✅ Variables

Now we learn:

# Methods

A method is simply a function inside a class.

---

# Real-Life Example

Suppose we have a Student.

A student has:

### Data

```text id="m9jlwm"
Name
Roll Number
Branch
```

### Actions

```text id="ucn2rb"
Attend Class
Write Exam
Pay Fee
View Result
```

In OOP:

```text id="8ktx0n"
Data → Variables

Actions → Methods
```

---

# Example

```python id="9hmlp3"
class Student:

    def study(self):
        print("Student is studying")
```

Here:

```python id="a1gk7d"
study()
```

is a method.

---

# Why Different Types of Methods?

Imagine a college.

Some tasks belong to:

### Individual Student

```text id="8e0bxy"
View Marks
Pay Fee
Attend Class
```

---

Some tasks belong to:

### Entire College

```text id="cvnsv3"
Change College Name
Update Academic Calendar
```

---

Some tasks are:

### Utility Functions

```text id="8wz6s0"
Calculate Percentage
Calculate GPA
```

These don't need student data.

---

Therefore Python provides:

```text id="i9tb7v"
1. Instance Method

2. Class Method

3. Static Method
```

---

# 1. Instance Method

Most commonly used method.

---

# Definition

A method that works with object data.

Uses:

```python id="hhl6e5"
self
```

---

# Real-Life Example

Student wants to view his name.

The method needs object data.

Therefore use instance method.

---

# Example

```python id="gbjlwm"
class Student:

    def __init__(self, name):

        self.name = name

    def display(self):

        print(self.name)
```

Object:

```python id="qjlwm7"
student1 = Student("Rahul")

student1.display()
```

Output:

```text id="f3u1ep"
Rahul
```

---

# Why Instance Method?

Because:

```python id="84i9mj"
self.name
```

belongs to an object.

---

# Internal Working

```python id="b0g6k7"
student1.display()
```

becomes:

```python id="2ccmfh"
Student.display(student1)
```

Therefore:

```python id="z3nxh2"
self = student1
```

---

# Real-Time Banking Example

```python id="sb7h7s"
class BankAccount:

    def __init__(self, balance):

        self.balance = balance

    def show_balance(self):

        print(self.balance)
```

Each account has different balance.

Therefore:

```python id="a0k1l8"
show_balance()
```

must be an instance method.

---

# Characteristics

✅ Uses self

✅ Accesses instance variables

✅ Called using object

```python id="vgn3rb"
object.method()
```

---

# 2. Class Method

Now comes class method.

---

# Real-Life Example

Suppose every student belongs to:

```text id="cb12a7"
ABC Engineering College
```

College name is shared by everyone.

It is a class variable.

Suppose management changes:

```text id="n8twru"
ABC Engineering College

to

XYZ Engineering College
```

Should we update every student individually?

❌ No

Update once.

---

# Class Method

Used to work with class variables.

Uses:

```python id="c4x1hg"
cls
```

instead of:

```python id="1tnm7m"
self
```

---

# Syntax

```python id="mjlwm8"
@classmethod

def method_name(cls):
```

---

# Example

```python id="n5u3wq"
class Student:

    college = "ABC College"

    @classmethod
    def change_college(cls):

        cls.college = "XYZ College"
```

Calling:

```python id="r4t9yb"
Student.change_college()
```

---

Output:

```text id="8dyk5i"
XYZ College
```

---

# What is cls?

Think:

```text id="m4h57l"
self → Current Object

cls → Current Class
```

---

# Visualization

```text id="1sjc39"
Student

     ↑

    cls
```

---

# Real-Time Example

VillageBasket:

```python id="3gf2qn"
class Customer:

    company_name = "VillageBasket"
```

If company rebrands:

```text id="yyqjl8"
VillageBasket

to

VillageMart
```

Use class method.

---

# Characteristics

✅ Uses cls

✅ Accesses class variables

✅ Called using class

```python id="7p2d6f"
ClassName.method()
```

---

# 3. Static Method

This is the easiest method.

---

# Real-Life Example

Suppose you need:

```text id="2chvku"
Percentage Calculation

BMI Calculation

Simple Interest Calculation
```

Do these calculations require:

```text id="3fny3h"
self ?
```

No.

---

Do they require:

```text id="ihf3i9"
cls ?
```

No.

---

Then why involve object or class?

No need.

Use static method.

---

# Definition

A method that neither uses object data nor class data.

---

# Syntax

```python id="2n5b7g"
@staticmethod

def method_name():
```

---

# Example

```python id="tb8jvn"
class Calculator:

    @staticmethod
    def add(a, b):

        return a + b
```

Calling:

```python id="p0nt6k"
print(Calculator.add(10, 20))
```

Output:

```text id="dx0ybo"
30
```

---

# Why Static Method?

Because:

```python id="3zyv7s"
10 + 20
```

doesn't depend on:

```text id="5pc6v5"
Object
```

or

```text id="whh5mi"
Class
```

---

# Real-Time Example

Student GPA Calculation

```python id="lg6o4u"
class Student:

    @staticmethod
    def calculate_percentage(total, marks):

        return (marks / total) * 100
```

Calling:

```python id="zbxdfw"
Student.calculate_percentage(500, 450)
```

Output:

```text id="0sk5t8"
90.0
```

---

# Complete Comparison

## Instance Method

Works with:

```python id="kg0htm"
self
```

Object Data

Example:

```python id="p0uj3y"
show_balance()
```

---

## Class Method

Works with:

```python id="f7ug9j"
cls
```

Class Data

Example:

```python id="sjd0yc"
change_college()
```

---

## Static Method

Works with:

```text id="0lrpkz"
Neither self nor cls
```

Utility Logic

Example:

```python id="6bd94t"
calculate_percentage()
```

---

# Bank Example

```python id="mofc7f"
class Bank:

    bank_name = "SBI"

    def __init__(self, balance):

        self.balance = balance
```

---

### Instance Method

```python id="ctv6um"
def show_balance(self):
```

Shows customer's balance.

---

### Class Method

```python id="az03gd"
@classmethod

def change_bank_name(cls):
```

Changes bank name.

---

### Static Method

```python id="6r5bwv"
@staticmethod

def calculate_interest():
```

Performs calculation.

---

# Interview Questions

### Which method uses self?

```text id="mqknje"
Instance Method
```

---

### Which method uses cls?

```text id="44gk6t"
Class Method
```

---

### Which decorator is used for class methods?

```python id="l6l6oz"
@classmethod
```

---

### Which decorator is used for static methods?

```python id="5pwoyz"
@staticmethod
```

---

### Can static methods access instance variables?

❌ No

Because they don't have self.

---

### Can static methods access class variables directly through cls?

❌ No

Because they don't have cls.

---

# Easy Memory Trick

Think:

### Instance Method

```text id="ow9nqw"
Me
```

Works with current object.

---

### Class Method

```text id="3hbdhf"
All of Us
```

Works with entire class.

---

### Static Method

```text id="j69v34"
Independent
```

Needs neither object nor class.

---

# Golden Rule

Ask this question:

### Does the method need object data?

YES →

```python id="hzysbl"
Instance Method
```

---

### Does the method need class data?

YES →

```python id="32dwah"
Class Method
```

---

### Does the method need neither?

YES →

```python id="lq3e9c"
Static Method
```

---

## Next Topic:

# Encapsulation – Data Hiding and Data Protection

This is the first pillar of OOP.

We will cover:

* Public Variables
* Protected Variables
* Private Variables
* Name Mangling
* Getter and Setter Methods

using **ATM PIN**, **Bank Account**, and **Student Result System** examples so students understand why companies never expose sensitive data directly.
