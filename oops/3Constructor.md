# OOPs Concept 3: Constructor (`__init__`) Method – Giving Life to Objects

In the previous class, we learned:

```python
class Student:
    pass

student1 = Student()
```

A student object is created.

But there is a problem.

---

# Real-Life Problem

Suppose a new student joins your college.

Immediately, you need:

* Name
* Roll Number
* Branch

Without these details, can the student be identified?

❌ No

Similarly, when an object is created, we usually want it to have some initial data.

For example:

```text
Student
    Name = Rahul
    Roll = 101
    Branch = CSE
```

How do we automatically assign these values when the object is created?

This is where the **Constructor** comes in.

---

# Real-Time Example: Buying a Mobile Phone

Imagine you buy a new phone.

When the phone comes from the factory, it already has:

* RAM
* Storage
* Processor
* IMEI Number

These details are assigned during manufacturing.

Similarly, when an object is created, we may want to initialize it with data immediately.

The constructor does that job.

---

# What is a Constructor?

A constructor is a special method that runs automatically when an object is created.

In Python, the constructor method is:

```python
__init__()
```

Pronunciation:

```text
dunder init
```

or

```text
double underscore init double underscore
```

---

# Why Do We Need a Constructor?

Without constructor:

```python
class Student:
    pass

student1 = Student()
```

Student object is created.

But:

```text
Name ?
Roll Number ?
Branch ?
```

Nothing is stored.

---

With constructor:

```python
class Student:
    def __init__(self):
        self.name = "Rahul"
        self.roll = 101
        self.branch = "CSE"
```

Now every object gets data automatically.

---

# Constructor Syntax

```python
class ClassName:

    def __init__(self):
        # initialization code
```

---

# Understanding the Parts

```python
def __init__(self):
```

### `__init__`

Special constructor method.

Runs automatically when object is created.

---

### `self`

Represents the current object being created.

Think:

```text
self = "This Object"
```

Very important concept.

We will learn it deeply in the next topic.

For now remember:

```text
self refers to the current object.
```

---

# First Constructor Example

```python
class Student:

    def __init__(self):
        print("Student object created")

student1 = Student()
```

Output:

```text
Student object created
```

Why?

Because constructor runs automatically.

---

# Step-by-Step Execution

### Step 1

Python sees:

```python
student1 = Student()
```

---

### Step 2

Object is created.

---

### Step 3

Python automatically calls:

```python
__init__()
```

---

### Step 4

Print statement executes.

```text
Student object created
```

---

# Creating Multiple Objects

```python
class Student:

    def __init__(self):
        print("Constructor Executed")

student1 = Student()
student2 = Student()
student3 = Student()
```

Output:

```text
Constructor Executed
Constructor Executed
Constructor Executed
```

Observation:

Constructor runs once for every object created.

---

# Real-Time Example: ATM Card

When a bank creates an ATM card:

The card immediately receives:

```text
Card Number
Account Number
PIN
Customer Name
```

Similarly, constructor initializes object data.

---

# Constructor Storing Data

```python
class Student:

    def __init__(self):
        self.name = "Rahul"
        self.roll = 101

student1 = Student()

print(student1.name)
print(student1.roll)
```

Output:

```text
Rahul
101
```

---

# Real-Time Example: Food Delivery App

Imagine VillageBasket customer registration.

When customer object is created:

```text
Customer Name
Mobile Number
Address
```

should be stored immediately.

```python
class Customer:

    def __init__(self):
        self.name = "Narendar"
        self.city = "Hyderabad"

customer1 = Customer()

print(customer1.name)
print(customer1.city)
```

Output:

```text
Narendar
Hyderabad
```

---

# Constructor with Dynamic Values

The previous examples stored fixed values.

But every student is different.

Rahul:

```text
Roll = 101
```

Priya:

```text
Roll = 102
```

Kiran:

```text
Roll = 103
```

How can we pass different values?

---

# Constructor Parameters

```python
class Student:

    def __init__(self, name, roll):
        self.name = name
        self.roll = roll
```

Creating objects:

```python
student1 = Student("Rahul", 101)
student2 = Student("Priya", 102)
student3 = Student("Kiran", 103)

print(student1.name)
print(student2.name)
print(student3.name)
```

Output:

```text
Rahul
Priya
Kiran
```

---

# Visualization

```text
student1 = Student("Rahul",101)

            ↓

self.name = "Rahul"
self.roll = 101
```

Object now contains:

```text
student1
 ├── name = Rahul
 └── roll = 101
```

---

# Constructor vs Normal Method

### Constructor

```python
def __init__(self):
```

Runs automatically.

---

### Normal Method

```python
def display(self):
```

Must be called manually.

Example:

```python
class Student:

    def __init__(self):
        print("Constructor")

    def display(self):
        print("Display Method")

student1 = Student()

student1.display()
```

Output:

```text
Constructor
Display Method
```

Notice:

Constructor executes automatically.

Display method executes only when called.

---

# Important Interview Questions

### What is a Constructor?

A constructor is a special method that executes automatically when an object is created and is used to initialize object data.

---

### What is the constructor name in Python?

```python
__init__()
```

---

### Can a constructor return a value?

No.

A constructor initializes objects and should not return normal values.

---

### When is a constructor called?

Automatically during object creation.

---

# Most Important Real-Life Analogy

### Class

Blueprint of a House

---

### Object

Actual House

---

### Constructor

The process of setting up:

* Doors
* Windows
* Electricity
* Water Connection

before the owner enters.

Without setup, the house is incomplete.

Without constructor, the object is often incomplete.

---

# Key Takeaway

```text
Class        → Blueprint

Object       → Real Instance

Constructor  → Initializes the Object
```

When Python sees:

```python
student1 = Student("Rahul", 101)
```

It automatically calls:

```python
__init__(self, "Rahul", 101)
```

and stores the data inside the object.

---

### Next Topic:

**The Most Important OOP Concept: `self` Keyword — What it really is, why Python needs it, and how it connects each object to its own data.**

This topic is where many beginners get confused, so we'll cover it with ATM, Student, Mobile Phone, and VillageBasket examples.
