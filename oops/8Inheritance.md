# OOPs Concept 8: Inheritance – Reusing Existing Code

This is the **Second Pillar of OOP** and one of the most powerful concepts.

Before learning the definition, let's understand a real-life situation.

---

# Real-Life Problem

Imagine you are developing **VillageBasket**.

You have three types of users:

```text
Customer
Delivery Partner
Admin
```

All have:

```text
Name
Phone Number
Email
Login()
Logout()
```

Without Inheritance:

```python
class Customer:
    name
    phone
    email

class DeliveryPartner:
    name
    phone
    email

class Admin:
    name
    phone
    email
```

Same code repeated again and again.

This violates a very important programming principle:

```text
Don't Repeat Yourself (DRY)
```

---

# Real-Life Example

Think about a family.

Suppose:

Father has:

```text
Brown Eyes
Black Hair
```

Child may also have:

```text
Brown Eyes
Black Hair
```

The child inherits characteristics from parents.

Similarly in programming:

A child class can inherit properties and methods from a parent class.

This concept is called:

# Inheritance

---

# Definition

Inheritance is a mechanism through which one class acquires the properties and methods of another class.

Simple words:

```text
Reuse Existing Code
```

---

# Terminology

### Parent Class

Also called:

```text
Base Class
Super Class
```

---

### Child Class

Also called:

```text
Derived Class
Sub Class
```

---

# First Example

Parent Class:

```python
class Person:

    def display(self):

        print("I am a Person")
```

Child Class:

```python
class Student(Person):
    pass
```

Object:

```python
student1 = Student()

student1.display()
```

Output:

```text
I am a Person
```

---

# What Happened?

Student class does not have:

```python
display()
```

method.

Python looks in parent class:

```python
Person
```

and finds it.

This is inheritance.

---

# Visualization

```text
           Person
              │
              │
              ▼
           Student
```

Student automatically gets:

```text
display()
```

---

# Real-Time Example: College

Parent:

```python
class Person:

    def __init__(self, name):

        self.name = name
```

Child:

```python
class Student(Person):

    def study(self):

        print("Studying")
```

Object:

```python
student1 = Student("Rahul")

print(student1.name)

student1.study()
```

Output:

```text
Rahul
Studying
```

Notice:

```text
name
```

came from parent class.

```text
study()
```

came from child class.

---

# Why Inheritance?

Without inheritance:

```python
class Customer:

    name
    phone
    email

class DeliveryPartner:

    name
    phone
    email

class Admin:

    name
    phone
    email
```

Repeated code.

---

With inheritance:

```python
class User:

    name
    phone
    email
```

Child classes:

```python
class Customer(User):
    pass

class DeliveryPartner(User):
    pass

class Admin(User):
    pass
```

Now code is cleaner.

---

# Real-Time VillageBasket Example

Parent Class:

```python
class User:

    def login(self):

        print("Login Successful")

    def logout(self):

        print("Logout Successful")
```

Child:

```python
class Customer(User):

    def order_food(self):

        print("Food Ordered")
```

Object:

```python
customer = Customer()

customer.login()

customer.order_food()
```

Output:

```text
Login Successful
Food Ordered
```

Customer gets:

```text
login()
logout()
```

for free.

---

# Types of Inheritance

Python supports 5 types.

---

# 1. Single Inheritance

One Parent

One Child

```text
Person
   │
   ▼
Student
```

Example:

```python
class Person:
    pass

class Student(Person):
    pass
```

Most common type.

---

# 2. Multilevel Inheritance

Grandparent → Parent → Child

```text
Person
   │
   ▼
Employee
   │
   ▼
Manager
```

Example:

```python
class Person:
    pass

class Employee(Person):
    pass

class Manager(Employee):
    pass
```

Manager gets features from both.

---

# 3. Multiple Inheritance

One child inherits from multiple parents.

```text
Teacher      Researcher
      \      /
       \    /
        ▼  ▼
        Professor
```

Example:

```python
class Teacher:
    pass

class Researcher:
    pass

class Professor(Teacher, Researcher):
    pass
```

---

# 4. Hierarchical Inheritance

One parent.

Many children.

```text
          User
        /  |  \
       /   |   \
Customer Admin DeliveryPartner
```

VillageBasket uses this heavily.

---

# 5. Hybrid Inheritance

Combination of multiple inheritance types.

```text
Person
   │
Employee
 /     \
Manager Developer
```

Used in large systems.

---

# The super() Method

Now comes an important interview topic.

---

# Problem

Parent Constructor:

```python
class Person:

    def __init__(self, name):

        self.name = name
```

Child Constructor:

```python
class Student(Person):

    def __init__(self, name, roll):

        self.roll = roll
```

Object:

```python
student1 = Student("Rahul", 101)
```

Problem:

```text
name
```

is never initialized.

---

# Solution: super()

```python
class Student(Person):

    def __init__(self, name, roll):

        super().__init__(name)

        self.roll = roll
```

Object:

```python
student1 = Student("Rahul", 101)

print(student1.name)

print(student1.roll)
```

Output:

```text
Rahul
101
```

---

# What Does super() Mean?

Think:

```text
super()
```

means:

```text
Go to Parent Class
```

---

# Visualization

```text
Student Constructor

       │

       ▼

super()

       │

       ▼

Person Constructor
```

---

# Method Overriding

Child class can replace parent method.

---

Example:

```python
class Person:

    def intro(self):

        print("I am a Person")
```

Child:

```python
class Student(Person):

    def intro(self):

        print("I am a Student")
```

Object:

```python
student1 = Student()

student1.intro()
```

Output:

```text
I am a Student
```

Parent method is overridden.

---

# Real-Time Example

Parent:

```python
class User:

    def dashboard(self):

        print("General Dashboard")
```

Child:

```python
class Admin(User):

    def dashboard(self):

        print("Admin Dashboard")
```

Admin gets its own dashboard.

---

# Interview Questions

### What is Inheritance?

Inheritance is a mechanism that allows one class to acquire properties and methods of another class.

---

### Why use Inheritance?

```text
Code Reusability
```

and

```text
Reduced Duplication
```

---

### What is a Parent Class?

Class whose properties are inherited.

---

### What is a Child Class?

Class that inherits from another class.

---

### What does super() do?

Used to access parent class members.

---

### Which OOP Pillar is Inheritance?

```text
Second Pillar
```

---

# Real-Time Example Summary

### Parent Class

```text
User
```

Contains:

```text
Login
Logout
Name
Email
Phone
```

---

### Child Classes

```text
Customer
Admin
Delivery Partner
```

Get common functionality from User.

---

# Golden Rule

Whenever you see:

```text
Many classes sharing common code
```

Think:

```text
Inheritance
```

Instead of copying code repeatedly.

---

# Key Takeaway

```text
Inheritance = Reuse Existing Code
```

```text
Parent Class
      │
      ▼
Child Class
```

Child automatically gets:

✅ Variables

✅ Methods

✅ Constructors (through `super()`)

from the Parent Class.

---

## Next Topic:

# Polymorphism – The Third Pillar of OOP

We'll cover:

* What is Polymorphism?
* Method Overloading (Python style)
* Method Overriding
* Duck Typing
* Operator Overloading

using examples from Students, Animals, Vehicles, and VillageBasket. This is where students learn how **one interface can behave differently in different situations**.
