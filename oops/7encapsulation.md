# OOPs Concept 7: Encapsulation – Data Hiding & Data Protection

This is the **First Pillar of OOP**.

Before teaching the definition, let's understand a real-life situation.

---

# Real-Life Example: ATM Machine

Imagine you go to an ATM.

You enter:

```text
ATM PIN = 1234
```

Question:

Can you see everyone else's PIN?

❌ No

Can you directly access the bank database and change your balance?

❌ No

Can you directly modify your account information without authorization?

❌ No

Why?

Because the bank hides sensitive data.

This concept is called:

# Encapsulation

---

# Another Real-Life Example

Suppose a college stores student marks.

```text
Rahul = 85

Priya = 92

Kiran = 78
```

Can a student directly enter the database and change:

```text
85 → 100
```

❌ No

The college protects the data.

---

# Why Encapsulation?

Without protection:

```python
balance = 10000

balance = 10000000
```

Anyone can modify data.

This is dangerous.

---

# Definition

Encapsulation means:

> Wrapping data and methods into a single unit and controlling access to that data.

Simple words:

```text
Data + Security = Encapsulation
```

---

# Real-Life Analogy

Think of a medicine capsule.

Inside capsule:

```text
Medicine
```

Outside capsule:

```text
Protective Cover
```

Similarly:

```text
Data
```

is protected by

```text
Methods
```

---

# Encapsulation in Python

Python provides 3 access levels:

```text
1. Public
2. Protected
3. Private
```

---

# 1. Public Members

Default access level.

Everyone can access them.

---

## Example

```python
class Student:

    def __init__(self):

        self.name = "Rahul"
```

Object:

```python
student1 = Student()

print(student1.name)
```

Output:

```text
Rahul
```

---

# Visualization

```text
Student

name → Public
```

Everyone can access it.

---

# Real-Life Example

College Name

```text
ABC Engineering College
```

Anyone can know it.

No need to hide.

---

# 2. Protected Members

Protected variables start with:

```python
_
```

(single underscore)

---

## Example

```python
class Student:

    def __init__(self):

        self._roll = 101
```

Object:

```python
student1 = Student()

print(student1._roll)
```

Output:

```text
101
```

Still accessible.

---

# Then Why Protected?

Protected is a convention.

Python is telling developers:

```text
Please don't access this directly.
```

It is intended for internal use.

---

# Real-Life Example

Teacher's internal remarks.

```text
Student Performance Notes
```

Students technically may see them someday.

But they are intended for staff use.

---

# 3. Private Members

Now comes the important one.

Private variables start with:

```python
__
```

(double underscore)

---

## Example

```python
class BankAccount:

    def __init__(self):

        self.__pin = 1234
```

Object:

```python
account = BankAccount()

print(account.__pin)
```

Output:

```text
AttributeError
```

Python won't allow direct access.

---

# Real-Life Example

ATM PIN

```text
1234
```

Should customers see everyone's PIN?

❌ No

Therefore:

```python
__pin
```

Private variable.

---

# Name Mangling

Many students think:

```python
__pin
```

becomes completely invisible.

Not exactly.

Python internally changes:

```python
__pin
```

to:

```python
_BankAccount__pin
```

This process is called:

# Name Mangling

---

## Example

```python
class BankAccount:

    def __init__(self):

        self.__pin = 1234

account = BankAccount()

print(account._BankAccount__pin)
```

Output:

```text
1234
```

---

# Why Name Mangling?

Python's goal:

```text
Protect from accidental access.
```

Not:

```text
Provide military-level security.
```

---

# Problem Without Encapsulation

Imagine:

```python
class BankAccount:

    def __init__(self):

        self.balance = 10000
```

Anyone can do:

```python
account.balance = 99999999
```

Dangerous.

---

# Solution: Private Variable

```python
class BankAccount:

    def __init__(self):

        self.__balance = 10000
```

Now balance is protected.

---

# Getter Methods

Suppose customer wants to view balance.

We should allow viewing.

Use a Getter Method.

---

## Example

```python
class BankAccount:

    def __init__(self):

        self.__balance = 10000

    def get_balance(self):

        return self.__balance
```

Object:

```python
account = BankAccount()

print(account.get_balance())
```

Output:

```text
10000
```

---

# Real-Life Example

ATM Balance Enquiry

```text
View Balance
```

Allowed.

Therefore:

```python
get_balance()
```

---

# Setter Methods

Suppose we want controlled updates.

---

## Example

```python
class BankAccount:

    def __init__(self):

        self.__balance = 10000

    def set_balance(self, amount):

        if amount > 0:

            self.__balance = amount
```

Object:

```python
account = BankAccount()

account.set_balance(20000)
```

Output:

```text
Balance Updated
```

---

# Why Setter?

Without validation:

```python
balance = -5000
```

Possible.

With setter:

```python
if amount > 0
```

Invalid values are rejected.

---

# Complete Example

```python
class BankAccount:

    def __init__(self):

        self.__balance = 10000

    def get_balance(self):

        return self.__balance

    def set_balance(self, amount):

        if amount > 0:

            self.__balance = amount

        else:

            print("Invalid Amount")
```

---

# VillageBasket Example

Customer data:

```text
Name
Address
Phone
```

Public.

---

Sensitive data:

```text
Password
OTP
Wallet Balance
```

Private.

---

Example:

```python
class Customer:

    def __init__(self):

        self.__password = "abc123"
```

Password should never be directly accessible.

---

# Interview Questions

### What is Encapsulation?

Encapsulation is the process of binding data and methods together while controlling access to data.

---

### What are the three access levels in Python?

```text
Public
Protected
Private
```

---

### How is a Protected variable created?

```python
_variable
```

---

### How is a Private variable created?

```python
__variable
```

---

### What is Name Mangling?

Python internally changes:

```python
__variable
```

to:

```python
_ClassName__variable
```

This process is called Name Mangling.

---

### Why use Getter and Setter methods?

To provide controlled access to private data.

---

# Easy Memory Trick

### Public

```text
Everyone Can Access
```

Example:

```text
College Name
```

---

### Protected

```text
Use Carefully
```

Example:

```text
Teacher Internal Notes
```

---

### Private

```text
Highly Sensitive
```

Example:

```text
ATM PIN
Password
Bank Balance
```

---

# Golden Rule

If the data is sensitive:

```python
__private_variable
```

Use Encapsulation.

Never expose:

```text
Password
PIN
OTP
Bank Balance
Medical Records
```

directly to users.

---

## Next Topic:

# Inheritance – The Second Pillar of OOP

We will learn:

* Why inheritance exists
* Parent Class
* Child Class
* Types of Inheritance
* `super()` method

using real-world examples like:

* Student → Graduate Student
* Vehicle → Car → Electric Car
* User → Customer → Delivery Partner → Admin

This is where OOP starts becoming truly powerful.
