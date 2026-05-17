## Concept 2: Data Types (Placement-Oriented + Industry + Hidden Concepts)

### Goal of this Module

By end of class students should:

✅ Know all Python data types
✅ Understand mutable vs immutable deeply
✅ Predict tricky outputs
✅ Handle interview questions confidently
✅ Avoid common bugs
✅ Understand memory references
✅ Solve placement coding questions

---

# 1. What are Data Types?

### Definition

A Data Type defines:

**What kind of value is stored in memory.**

Example:

```python
age = 24
```

Here:

`24` → Integer datatype

Why datatype?

Because computer needs to know:

* How much memory to allocate
* What operations are possible
* How data behaves internally

Example:

You cannot divide a string.

```python
name = "Narendar"

print(name / 2)
```

Error occurs.

---

# 2. Real World Analogy

Think of a supermarket.

Different sections:

* Vegetables
* Fruits
* Dairy
* Frozen Foods

Each section stores only specific items.

Same way:

Python organizes data into types.

---

# 3. Types of Data Types in Python

## Primitive Data Types

### 1. Integer (int)

Whole numbers

```python
age = 24
salary = 100000
negative = -50
```

Interview Hidden Point:

Python integers have **unlimited size**.

Unlike Java:

Java:

```java
int x = 2147483647
```

Overflow possible.

Python:

```python
x = 99999999999999999999999999999999999999
print(x)
```

No overflow.

### Interview Question

**Why Python integers don’t overflow?**

### Answer

Python internally uses:

**Arbitrary Precision Arithmetic**

Meaning:

Python dynamically allocates memory.

---

## 2. Float

Decimal numbers

```python
cgpa = 8.56
price = 99.99
```

### Hidden Concept (Important)

Floating-point precision issue.

```python
print(0.1 + 0.2)
```

Output:

```python
0.30000000000000004
```

Ask students:

Why not exactly 0.3?

### Answer

Computer stores decimal numbers in **binary format**.

Some decimals cannot be represented perfectly.

This is based on:

IEEE 754

### Real Industry Fix

Use:

```python
round(0.1 + 0.2, 1)
```

OR

```python
from decimal import Decimal

print(Decimal("0.1") + Decimal("0.2"))
```

---

## 3. String (str)

Collection of characters.

```python
name = "Narendar"
college = 'GITAM'
```

### Hidden Concept

Strings are:

## Immutable

Meaning:

Cannot be changed after creation.

Example:

Wrong:

```python
name = "python"

name[0] = "P"
```

Error occurs.

Correct:

```python
name = "python"

name = "P" + name[1:]

print(name)
```

---

## 4. Boolean (bool)

True or False

```python
is_logged_in = True
is_paid = False
```

### Hidden Trick

```python
print(True + True)
```

Output:

```python
2
```

Why?

Answer:

Internally:

```python
True = 1
False = 0
```

Most students don’t know this.

---

## 5. Complex Numbers

Rare but interview question.

```python
x = 3 + 5j

print(type(x))
```

Used in:

* Signal processing
* ML mathematics
* Engineering

---

# 4. Non Primitive Data Types

---

## 1. List

Ordered, mutable collection.

```python
marks = [90, 80, 70]
```

Mutable:

```python
marks[0] = 100
```

Allowed.

### Hidden Interview Question

Why list is slower than tuple?

Answer:

Because list is mutable.

Extra memory required for modification.

---

## 2. Tuple

Ordered, immutable collection.

```python
marks = (90, 80, 70)
```

Cannot change values.

Faster than list.

### Hidden Trick

Tuple with one element:

Wrong:

```python
x = (10)
```

Type?

```python
int
```

Correct:

```python
x = (10,)
```

Output:

```python
tuple
```

Most students fail this.

---

## 3. Set

Unordered collection.

Unique values only.

```python
numbers = {1, 2, 3, 1}

print(numbers)
```

Output:

```python
{1,2,3}
```

### Hidden Trick

```python
print({} )
```

Output?

Not set.

It is:

```python
dict
```

Correct empty set:

```python
set()
```

Very important interview trap.

---

## 4. Dictionary

Key-value pair.

```python
student = {
    "name": "Narendar",
    "age": 24
}
```

### Hidden Interview Question

Can dictionary keys be mutable?

Answer:

No.

Wrong:

```python
data = {
    [1,2]: "Python"
}
```

Error.

Why?

Keys must be:

### Immutable & Hashable

Correct:

```python
data = {
    (1,2): "Python"
}
```

---

# 5. Type Checking

```python
age = 24

print(type(age))
```

### Better Interview Answer

Prefer:

```python
isinstance(age, int)
```

Why?

Works with inheritance.

Interviewers like this answer.

---

# 6. Memory Understanding (Most Important)

```python
a = [1,2,3]
b = a

b.append(4)

print(a)
```

Output?

```python
[1,2,3,4]
```

Why?

Because:

Both point to same memory.

### Hidden Concept

Reference Copying.

Memory diagram teaching is MUST.

Then show:

```python
import copy

a = [1,2,3]
b = copy.deepcopy(a)
```

Difference between:

* shallow copy
* deep copy

Very important for placements.

---

# 7. Mutable vs Immutable (Most Asked)

## Immutable

Cannot change.

* int
* float
* string
* tuple
* bool

## Mutable

Can change.

* list
* dict
* set

### Placement Question

Why mutable objects are dangerous?

### Answer

Shared references may unintentionally modify data.

Bug example:

```python
def add_item(items):
    items.append("new")

my_list = ["a"]

add_item(my_list)

print(my_list)
```

Unexpected modification.

Industry bug.

---

# 8. Tricky Output Questions

### Question 1

```python
x = "5"
y = 5

print(x == y)
```

Answer:

```python
False
```

---

### Question 2

```python
x = [1,2]
y = x

y.append(3)

print(x)
```

Answer:

```python
[1,2,3]
```

---

### Question 3

```python
x = (10)

print(type(x))
```

Answer:

```python
<class 'int'>
```

---

### Question 4

```python
print(bool(""))
```

Answer:

```python
False
```

Falsy values:

```python
0
0.0
[]
{}
()
None
False
""
```

Very important for interviews.

---

# 9. Placement Interview Questions + Answers

### Q1: Difference between List and Tuple?

### Answer

| List                     | Tuple       |
| ------------------------ | ----------- |
| Mutable                  | Immutable   |
| Slower                   | Faster      |
| More memory              | Less memory |
| Dynamic changes possible | Safer data  |

Industry:

Tuple for constants.

List for dynamic data.

---

### Q2: Mutable vs Immutable?

### Answer

Mutable objects can be modified after creation.

Immutable cannot.

Examples:
List → mutable
Tuple → immutable

---

### Q3: Why strings are immutable?

### Answer

Reasons:

1. Security
2. Performance optimization
3. Hashing support
4. Thread safety

This answer impresses interviewers.

---

### Q4: Difference between `==` and `is`?

### Answer

`==`

Checks value.

```python
a = [1,2]
b = [1,2]

print(a == b)
```

True

`is`

Checks memory location.

```python
print(a is b)
```

False

Very frequently asked.

---

### Q5: Why tuple faster than list?

### Answer

Tuple is immutable.

No resizing or modification overhead.

Less memory.

Faster iteration.

---

### Q6 (Rare & Advanced)

Why dictionary lookup is O(1)?

### Answer

Because dictionary uses:

### Hash Table

Key → Hash → Direct Access

Most students won't know this.

---

# 10. Live Practical Activity

### Student Data Manager

Store:

* Name
* Marks
* Skills
* CGPA
* IsPlaced

Using:

* string
* list
* float
* boolean
* dictionary

Example:

```python
student = {
    "name": "Narendar",
    "marks": [80, 90, 95],
    "cgpa": 8.7,
    "isPlaced": False
}

print(student)
```

---

# 11. Class Assignments

## Level 1 (Basic)

1. Store all primitive types.
2. Print datatype of variables.
3. Student profile program.

---

## Level 2 (Intermediate)

1. Grocery billing system
2. Employee management
3. Marks calculator

---

## Level 3 (Placement)

Predict outputs.

Mutable vs immutable debugging.

Fix datatype bugs.

---

## Level 4 (Real World)

Build:

### College Student Database

Store:

* Student details
* Skills
* Marks
* Placement status

Using nested dictionary + list.

---

# 12. Secret / Rare Concepts to Teach (Students Usually Don’t Know)

### 1. String Interning

```python
a = "hello"
b = "hello"

print(a is b)
```

Why True sometimes?

Python optimization.

---

### 2. Small Integer Caching

```python
a = 256
b = 256

print(a is b)
```

Usually True.

But:

```python
a = 1000
b = 1000

print(a is b)
```

Can differ.

Interview killer question.

---

### 3. Everything in Python is Object

Even integer:

```python
x = 5

print(type(x))
```

Output:

```python
<class 'int'>
```

Internally:

Object.

---

### End-of-Class Mini Assessment

Ask students:

1. Difference between mutable and immutable?
2. Why float precision issue?
3. Difference between `==` and `is`?
4. Why tuple faster?
5. Why dictionary lookup fast?
6. Explain memory reference issue.

**Next best concept after this: Operators** (placement-heavy with tricky questions).
