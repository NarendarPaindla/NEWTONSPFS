# OOPs Concept 2: Class and Object – The Heart of OOPs

Before learning inheritance, polymorphism, or anything else, students must clearly understand:

> **What is a Class?**
>
> **What is an Object?**

If this concept is strong, the rest of OOPs becomes very easy.

---

# Real-Life Story

Imagine you are a bike manufacturer.

You are launching a new bike called:

```text
Royal Enfield Classic 350
```

Before manufacturing bikes, engineers create a **design blueprint**.

The blueprint contains:

* Engine Type
* Fuel Tank Capacity
* Color Options
* Top Speed
* Mileage

But can you ride the blueprint?

❌ No

Can you start the blueprint?

❌ No

Can you fill petrol in the blueprint?

❌ No

Because it is only a design.

---

# What is a Class?

A **Class** is a blueprint, template, or design used to create objects.

### Real World Example

```text
Blueprint → Class
Actual Bike → Object
```

---

## Student Example

Suppose every student in a college has:

* Name
* Roll Number
* Branch

Instead of creating variables repeatedly:

```python
student1_name = "Rahul"
student2_name = "Priya"
student3_name = "Kiran"
```

We create a blueprint.

```python
class Student:
    pass
```

This blueprint is called a **Class**.

---

# Definition

### Class

A class is a user-defined blueprint that defines the properties and behaviors of an object.

---

# What is an Object?

An Object is a real instance created from a class.

Think:

```text
Class = House Plan

Object = Actual House
```

---

## Example

Blueprint:

```text
Student
```

Actual Students:

```text
Rahul
Priya
Kiran
```

These are objects.

---

# Real-Time Example

Imagine you are teaching a batch of 100 students.

Do you create 100 different class definitions?

❌ No

One class is enough.

```text
Student
```

From that class:

```text
Student 1
Student 2
Student 3
...
Student 100
```

are created.

These are objects.

---

# Visual Representation

```text
                CLASS

              Student
         ┌──────────────┐
         │ Name         │
         │ Roll Number  │
         │ Branch       │
         └──────────────┘


                  ↓

             OBJECTS

      Rahul      Priya      Kiran
```

---

# Creating a Class

Syntax:

```python
class Student:
    pass
```

Explanation:

```python
class
```

Keyword used to create a class.

```python
Student
```

Class name.

```python
pass
```

Temporary placeholder.

---

# Creating Objects

```python
class Student:
    pass

student1 = Student()
student2 = Student()
student3 = Student()
```

Output:

```text
3 different student objects created
```

---

# Memory Visualization

```text
Class

Student
   │
   │
   ├────► student1
   ├────► student2
   └────► student3
```

One blueprint.

Multiple objects.

---

# Real-Time Example: Food Delivery App

Suppose you are developing VillageBasket.

Customer blueprint:

```text
Customer
    Name
    Mobile
    Address
```

Class:

```python
class Customer:
    pass
```

Objects:

```python
customer1 = Customer()
customer2 = Customer()
customer3 = Customer()
```

Each customer is an object.

---

# Real-Time Example: Car Showroom

Class:

```text
Car
```

Objects:

```text
BMW
Audi
Tesla
Honda
```

All are objects created from the Car blueprint.

---

# Important Interview Question

### How many objects can be created from a class?

Answer:

```text
Unlimited
```

As long as memory is available.

Example:

```python
student1 = Student()
student2 = Student()
student3 = Student()
...
student10000 = Student()
```

---

# Class vs Object

| Class                             | Object             |
| --------------------------------- | ------------------ |
| Blueprint                         | Real Instance      |
| Logical Entity                    | Physical Entity    |
| Doesn't Occupy Significant Memory | Occupies Memory    |
| Used to Create Objects            | Created From Class |
| One Class                         | Many Objects       |

---

# Easy Analogy

Think about a cookie cutter.

### Cookie Cutter

```text
Class
```

### Cookies Made From It

```text
Objects
```

One cutter.

Many cookies.

---

# Why Do We Need Objects?

Without objects:

```python
student1_name
student2_name
student3_name
student4_name
student5_name
```

Code becomes messy.

With objects:

```python
student1
student2
student3
student4
student5
```

Everything stays organized.

---

# Most Important Point

Students often think:

```python
class Student:
    pass
```

means a student is created.

❌ Wrong

Only a blueprint is created.

Student is actually created when:

```python
student1 = Student()
```

This is called:

### Object Creation

or

### Instantiation

---

# Interview Question

### What is a Class?

A class is a blueprint or template used to create objects.

---

### What is an Object?

An object is an instance of a class that contains actual data and can perform actions defined by the class.

---

# Key Takeaway

```text
Class = Blueprint

Object = Real Thing Created Using Blueprint
```

Example:

```text
Class  → Student

Objects →
Rahul
Priya
Kiran
Ramesh
```

One Class → Many Objects.

---

### Next Topic:

**Constructor (`__init__`) Method – How Objects Get Their Data Automatically During Creation (with Student, ATM, and Food Delivery examples).**
