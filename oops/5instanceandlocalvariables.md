# OOPs Concept 5: Instance Variables vs Local Variables vs Class Variables

This is one of the most important OOP concepts.

Many students see:

```python
name
self.name
Student.college_name
```

and think all three are the same.

❌ They are completely different.

Today we will understand them using a **real college example**.

---

# Real-Life Story

Suppose you are teaching in a college.

There are 3 students.

```text
Rahul
Priya
Kiran
```

Each student has:

* Name
* Roll Number
* Mobile Number

These details are different for every student.

But all students belong to the same college:

```text
ABC Engineering College
```

Now think carefully.

### Different for Every Student

```text
Name
Roll Number
Mobile
```

### Same for Every Student

```text
College Name
```

This idea is the foundation of variables in OOP.

---

# 1. Local Variables

Let's start with the easiest one.

---

## What is a Local Variable?

A variable created inside a method and used only inside that method.

---

### Example

```python
class Student:

    def display(self):

        name = "Rahul"

        print(name)

student1 = Student()

student1.display()
```

Output:

```text
Rahul
```

---

## What is happening?

```python
name = "Rahul"
```

exists only inside:

```python
display()
```

After the method finishes:

```text
Variable Destroyed
```

---

# Real-Life Example

Imagine you calculate:

```text
Today's Attendance = 45
```

during a class.

Once the class ends, that temporary value is no longer needed.

This is like a local variable.

---

# Characteristics of Local Variables

✅ Created inside method

✅ Used only inside method

✅ Temporary

✅ Cannot be accessed outside method

---

### Example

```python
class Student:

    def display(self):

        name = "Rahul"

student1 = Student()

print(name)
```

Output:

```text
NameError
```

Because local variables exist only inside the method.

---

# 2. Instance Variables

Now comes the most important variable type.

---

# What is an Instance Variable?

A variable that belongs to a specific object.

Created using:

```python
self.variable_name
```

---

### Example

```python
class Student:

    def __init__(self, name):

        self.name = name
```

---

Creating objects:

```python
student1 = Student("Rahul")

student2 = Student("Priya")
```

Memory:

```text
student1
    name = Rahul

student2
    name = Priya
```

Notice:

Each object has its own copy.

---

# Real-Life Example

Student 1:

```text
Name = Rahul
Roll = 101
```

Student 2:

```text
Name = Priya
Roll = 102
```

Can Rahul's roll number be shared with Priya?

❌ No

Each student has separate details.

Therefore:

```text
Name
Roll Number
Mobile
Address
```

should be instance variables.

---

# Example

```python
class Student:

    def __init__(self, name, roll):

        self.name = name
        self.roll = roll
```

Objects:

```python
student1 = Student("Rahul", 101)

student2 = Student("Priya", 102)
```

Memory:

```text
student1
    name = Rahul
    roll = 101

student2
    name = Priya
    roll = 102
```

---

# Characteristics of Instance Variables

✅ Belong to object

✅ Separate copy for every object

✅ Created using self

✅ Stored in object memory

---

# Real-Time Banking Example

```text
Account 1
Balance = 10000

Account 2
Balance = 5000
```

Balance must be different for each account.

Therefore:

```python
self.balance
```

must be an instance variable.

---

# 3. Class Variables

Now comes another important concept.

---

# What is a Class Variable?

A variable shared by all objects.

Created directly inside class.

---

### Example

```python
class Student:

    college = "ABC Engineering College"
```

Notice:

No self.

No constructor.

Directly inside class.

---

# Why Do We Need Class Variables?

Suppose:

```text
Rahul
Priya
Kiran
```

all study in:

```text
ABC Engineering College
```

Will we store college name separately inside every object?

```text
Rahul -> ABC College

Priya -> ABC College

Kiran -> ABC College
```

Possible.

But waste of memory.

Instead:

Store once.

Share with everyone.

---

# Example

```python
class Student:

    college = "ABC Engineering College"
```

Objects:

```python
student1 = Student()

student2 = Student()

student3 = Student()
```

All objects can access:

```python
Student.college
```

Output:

```text
ABC Engineering College
```

---

# Visualization

```text
                 CLASS

Student
 ├── college = ABC College
 │
 ├── student1
 │      name = Rahul
 │
 ├── student2
 │      name = Priya
 │
 └── student3
        name = Kiran
```

---

# Complete Example

```python
class Student:

    college = "ABC Engineering College"

    def __init__(self, name):

        self.name = name
```

Objects:

```python
student1 = Student("Rahul")

student2 = Student("Priya")
```

Access:

```python
print(student1.name)

print(student2.name)

print(Student.college)
```

Output:

```text
Rahul
Priya
ABC Engineering College
```

---

# Student Example Summary

### Different for Every Student

```text
Name
Roll
Mobile
```

Instance Variables

---

### Same for Every Student

```text
College Name
```

Class Variable

---

### Temporary Calculation

```text
Attendance Today
```

Local Variable

---

# Quick Comparison Table

| Feature         | Local Variable   | Instance Variable | Class Variable |
| --------------- | ---------------- | ----------------- | -------------- |
| Created Inside  | Method           | Object            | Class          |
| Keyword         | Normal Variable  | self              | Class Name     |
| Shared?         | No               | No                | Yes            |
| Lifetime        | Method Execution | Object Lifetime   | Class Lifetime |
| Memory Location | Method           | Object            | Class          |

---

# Interview Questions

### What is an Instance Variable?

A variable belonging to an object.

Example:

```python
self.name
```

---

### What is a Class Variable?

A variable shared by all objects.

Example:

```python
college = "ABC College"
```

---

### What is a Local Variable?

A variable created inside a method and used only within that method.

---

### Which variable type uses self?

```text
Instance Variables
```

---

### Which variable type is shared among all objects?

```text
Class Variables
```

---

# Real-Time VillageBasket Example

### Instance Variables

```text
Customer Name
Phone Number
Address
Cart Items
```

Every customer has different values.

---

### Class Variable

```text
Company Name = VillageBasket
```

Same for every customer.

---

### Local Variable

```text
Today's Bill Calculation
```

Used temporarily inside a method.

---

# Golden Rule for Students

Whenever you ask:

> "Should this data be different for every object?"

If **YES** →

```python
self.variable
```

Instance Variable

If **NO** →

```python
class_variable
```

Class Variable

If it is temporary inside a method →

```python
variable
```

Local Variable

---

## Next Topic:

### Methods in Python OOP

1. Instance Methods
2. Class Methods (`@classmethod`)
3. Static Methods (`@staticmethod`)

We'll use a **Bank Account**, **Student Management System**, and **VillageBasket** examples to explain why Python has three different types of methods and when to use each one.
