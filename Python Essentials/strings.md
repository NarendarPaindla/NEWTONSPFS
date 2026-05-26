# STRINGS IN PYTHON

# Concept–1: Introduction to Strings + String Creation + Properties + Indexing + Slicing
---

# 1. What is a String?

## Definition

A String is:

> **A collection of characters enclosed inside quotes.**

Characters include:

* Alphabets
* Numbers
* Symbols
* Spaces

Example:

```python
name = "Narendar"
```

Here:

```text
Narendar
```

is a string.

---

## Real World Examples

### Example 1: Name

```python
name = "Rahul"
```

---

### Example 2: Email

```python
email = "abc@gmail.com"
```

---

### Example 3: Address

```python
address = "Hyderabad"
```

---

### Example 4: Phone Number

Even though phone contains numbers:

```python
phone = "9876543210"
```

Still string.

Why?

Because:

We don't do math.

Wrong:

```python
phone + 5
```

No meaning.

---

# 2. Why Strings are Important?

In real applications:

### 90% of data is string

Examples:

* Username
* Password
* Email
* Chat messages
* Product names
* Addresses
* Reviews
* Search text

Industry-heavy topic.

---

# 3. How to Create String?

Python supports:

## Method 1 → Single Quotes

```python
name = 'Newton'
```

---

## Method 2 → Double Quotes

```python
name = "Newton"
```

---

## Method 3 → Triple Quotes

Used for:

### Multi-line strings

```python
message = '''
Hello Students
Welcome to Python
'''
```

Output:

```text
Hello Students
Welcome to Python
```

---

## Hidden Interview Question

Difference between:

```python
''
""
```

### Answer

No difference.

Both create strings.

Use whichever improves readability.

---

# 4. String Properties (Very Important)

Strings are:

### 1. Ordered

### 2. Immutable

### 3. Indexed

### 4. Iterable

Must remember.

Interview favorite.

---

## Property 1: Ordered

Characters maintain sequence.

Example:

```python
name = "Python"
```

Stored as:

```text
P  y  t  h  o  n
```

Order matters.

Cannot randomly change.

---

## Property 2: Indexed

Each character has position.

Example:

```python
name = "Python"
```

```text
P   y   t   h   o   n
0   1   2   3   4   5
```

Called:

### Positive Indexing

---

## Property 3: Immutable

Means:

### Cannot modify directly

Example:

Wrong:

```python
name = "Python"

name[0] = "J"
```

Error:

```text
TypeError
```

Very important placement question.

We will learn deeply later.

---

## Property 4: Iterable

Can traverse using loops.

Example:

```python
for ch in "Python":
    print(ch)
```

Output:

```text
P
y
t
h
o
n
```

---

# 5. String Indexing (Most Important)

Index means:

### Position of character

Example:

```python
language = "Python"
```

Memory visualization:

```text
P   y   t   h   o   n
0   1   2   3   4   5
```

---

## Access Character

Syntax:

```python
string[index]
```

Example:

```python
language = "Python"

print(language[0])
```

Output:

```text
P
```

---

### More Examples

```python
print(language[1])
```

Output:

```text
y
```

---

```python
print(language[5])
```

Output:

```text
n
```

---

# Tricky Question

Output?

```python
name = "Python"

print(name[6])
```

### Answer

Error:

```text
IndexError
```

Why?

Maximum index:

```text
5
```

---

# Memory Trick

Formula:

## Length - 1

Example:

```python
Python
```

Length:

```text
6
```

Max index:

```text
5
```

---

# 6. Negative Indexing

Python supports reverse indexing.

Example:

```python
name = "Python"
```

```text
P   y   t   h   o   n
-6 -5 -4 -3 -2 -1
```

---

### Example

```python
print(name[-1])
```

Output:

```text
n
```

---

```python
print(name[-2])
```

Output:

```text
o
```

---

### Real Use Case

Last character extraction.

Example:

Email extension.

---

# Tricky Question

Output?

```python
name = "Python"

print(name[-7])
```

Answer:

```text
IndexError
```

---

# 7. String Slicing (Most Important)

Slicing means:

### Extracting part of string

Syntax:

```python
string[start:end]
```

Rule:

### End excluded

Must repeat 10 times.

---

## Example 1

```python
name = "Python"

print(name[0:2])
```

Output:

```text
Py
```

Why?

Index:

```text
0 → P
1 → y
```

2 excluded.

---

## Example 2

```python
print(name[2:5])
```

Output:

```text
tho
```

---

## Example 3

```python
print(name[:4])
```

Output:

```text
Pyth
```

Meaning:

Start automatically:

```text
0
```

---

## Example 4

```python
print(name[2:])
```

Output:

```text
thon
```

Meaning:

Till end.

---

## Example 5

```python
print(name[:])
```

Output:

```text
Python
```

Copy whole string.

---

# 8. Step Slicing

Syntax:

```python
string[start:end:step]
```

---

## Example

```python
name = "Python"

print(name[0:6:2])
```

Output:

```text
Pto
```

Why?

Jump:

```text
2 steps
```

---

## Odd Position Characters

```python
print(name[::2])
```

Output:

```text
Pto
```

---

## Even Position Characters

```python
print(name[1::2])
```

Output:

```text
yhn
```

---

# 9. Reverse String (Important)

Shortcut:

```python
[::-1]
```

Example:

```python
name = "Python"

print(name[::-1])
```

Output:

```text
nohtyP
```

Huge interview favorite.

---

# 10. String Length

Using:

```python
len()
```

Example:

```python
name = "Python"

print(len(name))
```

Output:

```text
6
```

---

# 11. Hands-On Practical Questions

## Level 1

### Question 1

Print first character.

Input:

```text
Python
```

Output:

```text
P
```

---

### Question 2

Print last character.

---

### Question 3

Print middle character.

---

### Question 4

Print first 3 characters.

---

### Question 5

Print last 3 characters.

---

## Level 2

### Question 6

Reverse string.

---

### Question 7

Print alternate characters.

Example:

```text
Python
```

Output:

```text
Pto
```

---

### Question 8

Print characters except first.

---

### Question 9

Print characters except last.

---

### Question 10

Print string length.

---

# Brain Twister Questions 😄

### Q1

Output?

```python
name = "Python"

print(name[0:100])
```

### Answer

```text
Python
```

No error.

Python safely handles.

---

### Q2

Output?

```python
name = "Python"

print(name[-1:-4])
```

Answer:

```text
(empty)
```

Why?

Wrong direction.

Need negative step.

---

### Q3

Output?

```python
name = "Python"

print(name[-1:-4:-1])
```

Output:

```text
noh
```

---

### Q4

Output?

```python
name = "Python"

print(name[::-2])
```

Output:

```text
nhy
```

---

### Q5

Output?

```python
name = "Python"

print(name[1:5:2])
```

Output:

```text
yh
```

---

# Placement Interview Questions + Answers

### Q1: What is string?

Collection of characters inside quotes.

---

### Q2: Why string immutable?

Memory optimization.

Security.

Performance.

---

### Q3: Difference between indexing and slicing?

Indexing:

Single character.

Slicing:

Multiple characters.

---

### Q4: Difference between positive and negative indexing?

Positive:

Left → Right

Negative:

Right → Left

---

### Q5: Why no error here?

```python
name[0:100]
```

Python safely adjusts range.

---
