# OOPs Concept 9: Polymorphism – One Thing, Many Forms

This is the **Third Pillar of OOP**.

Before learning the definition, let's understand a real-life example.

---

# Real-Life Example: A Single Person, Multiple Roles

Imagine a person named **Rahul**.

At home:

```text
Rahul → Son
```

At college:

```text
Rahul → Student
```

With friends:

```text
Rahul → Friend
```

At work:

```text
Rahul → Employee
```

Same person.

Different behaviors.

This is called:

# Polymorphism

---

# Meaning of Polymorphism

The word comes from:

```text
Poly = Many

Morph = Forms
```

Therefore:

```text
Polymorphism = Many Forms
```

---

# Definition

Polymorphism allows the same method or operation to behave differently depending on the object.

Simple words:

```text
One Interface

Different Behaviors
```

---

# Real-Life Example: Vehicle

Suppose all vehicles have:

```python
start()
```

method.

But:

### Bike

```text
Kick Start
```

### Car

```text
Key Start
```

### Electric Car

```text
Button Start
```

Same method:

```python
start()
```

Different behavior.

This is Polymorphism.

---

# Types of Polymorphism in Python

```text
1. Method Overriding

2. Method Overloading (Python Style)

3. Duck Typing

4. Operator Overloading
```

---

# 1. Method Overriding

Most important form of polymorphism.

---

# What is Method Overriding?

When a child class provides its own implementation of a parent method.

---

## Example

Parent:

```python
class Animal:

    def sound(self):

        print("Animal makes sound")
```

Child:

```python
class Dog(Animal):

    def sound(self):

        print("Dog Barks")
```

Object:

```python
dog = Dog()

dog.sound()
```

Output:

```text
Dog Barks
```

---

# What Happened?

Parent had:

```python
sound()
```

Child created its own:

```python
sound()
```

version.

Child method replaces parent method.

This is:

# Method Overriding

---

# Real-Time Example: VillageBasket

Parent:

```python
class User:

    def dashboard(self):

        print("General Dashboard")
```

---

Customer:

```python
class Customer(User):

    def dashboard(self):

        print("Customer Dashboard")
```

---

Admin:

```python
class Admin(User):

    def dashboard(self):

        print("Admin Dashboard")
```

---

Objects:

```python
customer.dashboard()

admin.dashboard()
```

Output:

```text
Customer Dashboard

Admin Dashboard
```

Same method.

Different behavior.

Polymorphism.

---

# 2. Method Overloading (Python Style)

Students coming from Java often ask:

> Can we create multiple methods with the same name?

In Java:

```java
add(int a,int b)

add(int a,int b,int c)
```

Possible.

---

In Python:

❌ Traditional Method Overloading is not supported.

---

Example:

```python
class Demo:

    def add(self,a,b):

        print(a+b)

    def add(self,a,b,c):

        print(a+b+c)
```

Object:

```python
obj = Demo()

obj.add(10,20,30)
```

Output:

```text
60
```

The first method is overwritten.

Python keeps only the latest definition.

---

# Python Way of Overloading

Use default arguments.

```python
class Demo:

    def add(self,a,b,c=0):

        print(a+b+c)
```

Calls:

```python
obj.add(10,20)

obj.add(10,20,30)
```

Output:

```text
30

60
```

---

# Real-Life Example

Calculator:

```python
add(10,20)

add(10,20,30)

add(10,20,30,40)
```

Same operation.

Different number of inputs.

---

# 3. Duck Typing

This is a very famous Python interview topic.

---

# Real-Life Story

Imagine you hire a person.

Do you ask:

```text
Are you a Java Developer?
```

No.

You ask:

```text
Can you do the work?
```

If yes:

You hire them.

---

Python follows the same philosophy.

---

# Definition

Python cares about behavior, not type.

---

# Example

```python
class Dog:

    def speak(self):

        print("Bark")
```

```python
class Cat:

    def speak(self):

        print("Meow")
```

Function:

```python
def make_sound(animal):

    animal.speak()
```

Calls:

```python
make_sound(Dog())

make_sound(Cat())
```

Output:

```text
Bark

Meow
```

---

Python never checks:

```text
Dog ?

Cat ?
```

It only checks:

```text
Does it have speak() ?
```

If yes:

Run it.

---

# Famous Rule

```text
If it walks like a duck

and

quacks like a duck

then

treat it as a duck.
```

This is Duck Typing.

---

# Real-Time Example

VillageBasket:

```python
Customer.pay()

Admin.pay()

Vendor.pay()
```

If all objects implement:

```python
pay()
```

Python is happy.

---

# 4. Operator Overloading

Now comes a very interesting topic.

---

# Problem

What does:

```python
10 + 20
```

mean?

Output:

```text
30
```

---

What does:

```python
"Hello" + "World"
```

mean?

Output:

```text
HelloWorld
```

---

Question:

How can the same operator:

```python
+
```

perform different tasks?

Answer:

# Operator Overloading

---

# Example

Numbers:

```python
10 + 20
```

Addition.

---

Strings:

```python
"Python" + "Java"
```

Concatenation.

---

Same operator.

Different behavior.

Polymorphism.

---

# Custom Operator Overloading

```python
class Student:

    def __init__(self, marks):

        self.marks = marks

    def __add__(self, other):

        return self.marks + other.marks
```

Objects:

```python
s1 = Student(90)

s2 = Student(80)

print(s1 + s2)
```

Output:

```text
170
```

---

# What Happened?

Python converts:

```python
s1 + s2
```

to:

```python
s1.__add__(s2)
```

---

# Real-Time Example

Student Marks:

```text
Rahul = 90

Priya = 80
```

Adding:

```python
s1 + s2
```

gives:

```text
170
```

---

# Comparison Table

| Type                 | Description                                                        |
| -------------------- | ------------------------------------------------------------------ |
| Method Overriding    | Child replaces parent method                                       |
| Method Overloading   | Same method with different arguments (Python style using defaults) |
| Duck Typing          | Focus on behavior, not type                                        |
| Operator Overloading | Same operator behaves differently                                  |

---

# Interview Questions

### What is Polymorphism?

Polymorphism allows one interface to have multiple implementations.

---

### What is Method Overriding?

When a child class provides its own version of a parent method.

---

### Does Python support traditional Method Overloading?

❌ No

Use:

```python
Default Arguments
```

or

```python
*args
```

instead.

---

### What is Duck Typing?

Python checks capabilities of an object instead of its type.

---

### What is Operator Overloading?

Giving additional meaning to operators using special methods.

---

### Which OOP Pillar is Polymorphism?

```text
Third Pillar
```

---

# Real-Time VillageBasket Summary

### Parent

```python
User.login()
```

---

### Customer

```python
login()
```

Customer-specific behavior.

---

### Admin

```python
login()
```

Admin-specific behavior.

---

Same method.

Different implementations.

That's Polymorphism.

---

# Easy Memory Trick

### Inheritance

```text
Acquire Features
```

---

### Polymorphism

```text
Different Behaviors
```

---

# Golden Rule

Whenever you see:

```text
Same Method Name

Different Results
```

Think:

```text
Polymorphism
```

---

# OOP Pillars Covered So Far

✅ Encapsulation → Protect Data

✅ Inheritance → Reuse Code

✅ Polymorphism → Many Behaviors

---

## Next Topic:

# Abstraction – The Fourth and Final Pillar of OOP

We will learn:

* What is Abstraction?
* Why Abstraction is needed?
* Abstract Classes
* Abstract Methods
* ABC Module
* Real-world examples:

  * ATM Machine
  * Car Driving
  * Mobile Phone
  * Food Delivery Apps

This topic completes the **4 Pillars of OOP** and makes students industry-ready for OOP interviews.
