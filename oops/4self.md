# OOPs Concept 4: The `self` Keyword – The Most Important OOP Concept

This is the topic where most students get confused.

If you understand **self**, then:

✅ Constructors become easy

✅ Methods become easy

✅ Objects become easy

✅ Entire OOPs becomes easy

---

# First Understand the Problem

Suppose there are 3 students.

```python id="vzdjlwm"
student1 = Student("Rahul", 101)
student2 = Student("Priya", 102)
student3 = Student("Kiran", 103)
```

Each student should have their own data.

```text id="i0x9jke"
student1
    Rahul
    101

student2
    Priya
    102

student3
    Kiran
    103
```

Question:

How does Python know which data belongs to which object?

Answer:

Using **self**

---

# Real-Life Example: Hostel Rooms

Imagine a hostel.

There are 3 rooms.

```text id="l3nvl84"
Room 101 → Rahul

Room 102 → Priya

Room 103 → Kiran
```

If someone says:

```text id="lzdjmb4"
My bed
My table
My cupboard
```

The word **My** refers to the current person speaking.

Similarly:

```python id="b0chzhy"
self
```

means

```text id="0nfgsyj"
Current Object
```

---

# Definition

### self

`self` is a reference to the current object.

Or simply:

```text id="cf2p7n7"
self = current object
```

---

# Why Do We Need self?

Suppose:

```python id="g99n9jp"
class Student:

    def __init__(self, name):
        self.name = name
```

When:

```python id="z4fw7vh"
student1 = Student("Rahul")
```

Python internally does:

```python id="mqq8fcp"
Student.__init__(student1, "Rahul")
```

Notice:

```python id="2gk02qw"
student1
```

is automatically passed to:

```python id="f6q1p0j"
self
```

Therefore:

```python id="ypwrvuw"
self.name = "Rahul"
```

means:

```python id="xxhf4vp"
student1.name = "Rahul"
```

---

# Visualization

```text id="n2b5pwq"
student1 = Student("Rahul")

        ↓

self = student1

self.name = Rahul

        ↓

student1.name = Rahul
```

---

# Example 1

```python id="twp7y14"
class Student:

    def __init__(self, name):
        self.name = name

student1 = Student("Rahul")

print(student1.name)
```

Output:

```text id="ljmll1g"
Rahul
```

---

# What Happens Internally?

Python converts:

```python id="p4glcwu"
student1 = Student("Rahul")
```

to:

```python id="rq4prha"
Student.__init__(student1, "Rahul")
```

So:

```python id="jlnkxzd"
self = student1
```

---

# Example 2: Multiple Objects

```python id="ehp5x4y"
class Student:

    def __init__(self, name):
        self.name = name

student1 = Student("Rahul")
student2 = Student("Priya")
student3 = Student("Kiran")
```

Memory:

```text id="gynk4i7"
student1.name → Rahul

student2.name → Priya

student3.name → Kiran
```

Every object has separate data.

Because `self` points to the correct object.

---

# Real-Time Example: ATM Cards

Suppose a bank issues 3 ATM cards.

```text id="aofpd7u"
ATM1 → Rahul

ATM2 → Priya

ATM3 → Kiran
```

Each ATM card stores its own:

* Account Number
* PIN
* Balance

Imagine Rahul checks balance.

The ATM should show Rahul's balance.

Not Priya's balance.

How?

The ATM identifies the current account.

Similarly:

```python id="7dqf5lh"
self
```

identifies the current object.

---

# Example 3: Customer App

```python id="9hzt2lw"
class Customer:

    def __init__(self, name):
        self.name = name
```

Objects:

```python id="jm8m7o5"
customer1 = Customer("Narendar")
customer2 = Customer("Ravi")
```

Memory:

```text id="z6mxhwr"
customer1.name → Narendar

customer2.name → Ravi
```

Because `self` connects data to the correct object.

---

# self Is Not a Keyword

Many students think:

```python id="8jkt0z0"
self
```

is a Python keyword.

❌ Wrong

It is just a variable name.

You can write:

```python id="snf8uvf"
class Student:

    def __init__(abc, name):
        abc.name = name
```

This works.

But nobody writes it.

Industry standard:

```python id="nqsvjvk"
self
```

Always use `self`.

---

# Example 4: Method Using self

```python id="4z6lwk4"
class Student:

    def __init__(self, name):
        self.name = name

    def display(self):
        print(self.name)
```

Object:

```python id="04zudgf"
student1 = Student("Rahul")

student1.display()
```

Output:

```text id="x8qlgyr"
Rahul
```

---

# Internal Working

Python converts:

```python id="hlh2h1r"
student1.display()
```

to:

```python id="lws6vph"
Student.display(student1)
```

Therefore:

```python id="g6m36w9"
self = student1
```

---

# Visualization

```text id="t2szt11"
student1.display()

          ↓

display(student1)

          ↓

self = student1

          ↓

print(student1.name)
```

Output:

```text id="u7l4f8g"
Rahul
```

---

# Most Common Beginner Mistake

Wrong:

```python id="63v8f4q"
class Student:

    def __init__(self, name):
        name = name
```

Why wrong?

Both variables are local variables.

Nothing gets stored inside the object.

---

Correct:

```python id="jlwmz5f"
class Student:

    def __init__(self, name):
        self.name = name
```

Now data is stored in the object.

---

# Easy Memory Trick

Think:

```text id="sxl4pyr"
self.name
```

means

```text id="8aqcbbz"
My Name
```

Example:

```python id="90ul4m8"
self.name = "Rahul"
```

means

```text id="r4h31o7"
My name is Rahul
```

---

# Real-Time VillageBasket Example

```python id="9vql5jn"
class Customer:

    def __init__(self, name, city):
        self.name = name
        self.city = city
```

Objects:

```python id="w3lax5w"
customer1 = Customer("Narendar", "Hyderabad")

customer2 = Customer("Ravi", "Vijayawada")
```

Memory:

```text id="o1nqek5"
customer1
    name = Narendar
    city = Hyderabad

customer2
    name = Ravi
    city = Vijayawada
```

`self` ensures the correct data goes into the correct customer object.

---

# Interview Questions

### What is self in Python?

`self` is a reference to the current object.

---

### Is self a keyword?

No.

It is a naming convention.

---

### Why is self required?

To access and store data belonging to the current object.

---

### Who passes self?

Python automatically passes the current object as the first argument.

---

# Most Important Understanding

When you write:

```python id="9nwyv7t"
student1.display()
```

Python internally does:

```python id="ztlqv0r"
Student.display(student1)
```

Therefore:

```python id="dcvfog8"
self = student1
```

This single line explains almost the entire purpose of `self`.

---

# Key Takeaway

```text id="5el4pcq"
self = Current Object
```

Whenever you see:

```python id="1dr3jlt"
self.name
```

Read it as:

```text id="llr8omr"
Current Object's Name
```

Whenever you see:

```python id="hqj8k5s"
self.city
```

Read it as:

```text id="vb3bqk8"
Current Object's City
```

This mindset makes OOPs much easier.

---

## Next Topic:

**Instance Variables vs Local Variables vs Class Variables**

This is another interview-favorite topic where students learn:

* Where data is stored
* Why `self.name` and `name` are different
* How data is shared or isolated between objects
* Real-world examples using Students, Employees, and Bank Accounts.
