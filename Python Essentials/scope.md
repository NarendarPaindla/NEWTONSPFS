## Concept 6: Scope (Placement-Oriented + Hidden Python Behavior)

### Goal of this Module

By end of class students should:

✅ Understand variable visibility
✅ Understand local vs global deeply
✅ Avoid scope bugs in interviews
✅ Learn LEGB rule (important)
✅ Understand closures basics
✅ Solve tricky placement questions
✅ Debug `UnboundLocalError`

---

# 1. What is Scope?

### Definition

Scope means:

### **Where a variable can be accessed in a program**

Simple meaning:

A variable is **not available everywhere**.

It depends on:

### Where it is created.

---

## Real World Analogy

Think of:

### College Access System

* Classroom notice → only classroom students
* Department notice → only department students
* College notice → everyone

Same concept:

Variables have access boundaries.

---

# 2. Why Scope Exists?

Without scope:

Everything would be accessible everywhere.

Problems:

❌ Variable conflicts
❌ Unexpected changes
❌ Hard debugging

Scope gives:

✅ Safety
✅ Better memory management
✅ Cleaner code

Interview answer.

---

# 3. Types of Scope in Python

Python follows:

## LEGB Rule

Order Python searches variable:

1. Local
2. Enclosing
3. Global
4. Built-in

Very important interview topic.

---

# 4. Local Scope

Variable inside function.

Accessible only there.

Example:

```python id="8w4vka"
def student():

    name = "Narendar"

    print(name)

student()
```

Works.

Outside:

```python id="w6m5c5"
print(name)
```

Error:

```python id="pgdy67"
NameError
```

Why?

Variable exists only inside function.

---

### Hidden Concept

Memory cleanup.

Local variables get destroyed after function execution.

Performance optimization.

Most students don’t know this.

---

# 5. Global Scope

Variable declared outside function.

Accessible everywhere.

Example:

```python id="6h13gu"
college = "GITAM"

def show():
    print(college)

show()
```

Output:

```python id="l57g53"
GITAM
```

---

## Hidden Trap

Output?

```python id="4gkq14"
x = 10

def test():
    print(x)

test()
```

Answer:

```python id="35qqag"
10
```

Why?

Python checks local first.

Not found.

Moves to global.

LEGB rule.

---

# 6. Local vs Global Conflict

Very important.

Example:

```python id="of4rtf"
x = 100

def demo():
    x = 50
    print(x)

demo()

print(x)
```

Output:

```python id="bb6dsm"
50
100
```

Why?

Two separate variables.

Different scopes.

---

### Hidden Interview Question

Output?

```python id="gw6o34"
x = 10

def test():
    print(x)
    x = 20

test()
```

Answer:

```python id="3vcbb9"
UnboundLocalError
```

Most students fail.

---

### Why Error?

Python sees:

```python id="k6lzih"
x = 20
```

inside function.

So:

Python treats `x` as local.

But:

Trying to access before assignment.

---

# 7. global Keyword

Used to modify global variable.

Example:

```python id="e4lbzv"
x = 10

def test():
    global x
    x = 50

test()

print(x)
```

Output:

```python id="shf14u"
50
```

---

## Industry Reality

### Avoid `global`

Why?

Bad practice.

Creates unpredictable bugs.

Hard debugging.

Bad scalability.

Professional code avoids it.

Interview answer.

---

# 8. Enclosing Scope (Nested Functions)

Very hidden concept.

Used in:

Closures
Decorators
Advanced Python

Example:

```python id="1jczg0"
def outer():

    x = 100

    def inner():
        print(x)

    inner()

outer()
```

Output:

```python id="2px4ps"
100
```

---

### LEGB Working

Python searches:

1. Local → not found
2. Enclosing → found

Done.

---

# 9. nonlocal Keyword

Most students never know this.

Used to modify enclosing variable.

Example:

```python id="r8jng8"
def outer():

    count = 0

    def inner():

        nonlocal count

        count += 1

        print(count)

    inner()

outer()
```

Output:

```python id="cz6csl"
1
```

---

### Interview Question

Difference between:

`global`

and

`nonlocal`

### Answer

`global`

Modifies global variable.

`nonlocal`

Modifies enclosing function variable.

---

# 10. Built-in Scope

Python predefined functions.

Example:

```python id="xk5dzm"
print()
len()
sum()
max()
min()
```

Works without defining.

Why?

Python built-ins.

---

### Hidden Trap

Output?

```python id="jj6hyy"
list = [1,2,3]

print(list("abc"))
```

Answer:

Error.

Why?

You overwrote built-in:

```python id="kkpkh0"
list
```

Very important industry bug.

Teach:

### Never use keywords/built-in names.

Bad:

```python id="jlwmgd"
list = []
str = "abc"
sum = 10
```

---

# 11. LEGB Rule (Most Important)

Example:

```python id="jlwmge"
x = "global"

def outer():

    x = "enclosing"

    def inner():

        x = "local"

        print(x)

    inner()

outer()
```

Output:

```python id="jlwmgf"
local
```

Search order:

Local → found.

Stops there.

---

### Another Example

```python id="jlwmgg"
x = "global"

def outer():

    x = "enclosing"

    def inner():
        print(x)

    inner()

outer()
```

Output:

```python id="jlwmgh"
enclosing
```

LEGB in action.

---

# 12. Closures (Advanced Placement Topic)

Very important for advanced companies.

Function remembers outer variables.

Example:

```python id="jlwmgi"
def outer(message):

    def inner():
        print(message)

    return inner

greet = outer("Hello")

greet()
```

Output:

```python id="jlwmgj"
Hello
```

Why?

Function remembers outer state.

Used in:

* Decorators
* APIs
* Logging
* Middleware

---

# 13. Scope Memory Understanding

Example:

```python id="jlwmgk"
def demo():

    x = [1,2]

    return x

a = demo()
```

After function ends:

Local destroyed.

But object survives because:

Still referenced.

Very hidden concept.

---

# 14. Tricky Placement Questions

### Question 1

Output?

```python id="jlwmgl"
x = 100

def test():
    x = 50

test()

print(x)
```

Answer:

```python id="jlwmgm"
100
```

---

### Question 2

Output?

```python id="jlwmgn"
x = 10

def test():
    global x
    x = 20

test()

print(x)
```

Answer:

```python id="jlwmgo"
20
```

---

### Question 3

Output?

```python id="jlwmgp"
x = 10

def test():
    print(x)
    x = 20

test()
```

Answer:

```python id="jlwmgq"
UnboundLocalError
```

---

### Question 4

Output?

```python id="jlwmgr"
def outer():

    x = 100

    def inner():
        print(x)

    inner()

outer()
```

Answer:

```python id="jlwmgs"
100
```

---

### Question 5

Output?

```python id="jlwmgt"
list = [1,2]

print(list("abc"))
```

Answer:

Error.

Because built-in overwritten.

Huge interview trap.

---

# 15. Placement Interview Questions + Answers

### Q1: What is scope?

### Answer

Scope defines:

### Where variable is accessible.

---

### Q2: Explain LEGB Rule.

### Answer

Python searches variables in order:

Local → Enclosing → Global → Built-in

---

### Q3: Difference between local and global?

### Answer

| Local           | Global                |
| --------------- | --------------------- |
| Inside function | Outside function      |
| Temporary       | Exists program-wide   |
| Limited access  | Accessible everywhere |

---

### Q4: Why global keyword bad?

### Answer

Hard debugging.

Unexpected modifications.

Poor scalability.

Professional systems avoid globals.

---

### Q5: Difference between global and nonlocal?

### Answer

`global`

Modifies global variable.

`nonlocal`

Modifies enclosing scope variable.

---

### Q6: What is closure?

### Answer

Function remembering variables from outer function even after outer finishes.

Advanced Python concept.

---

# 16. Live Practical Activity

### Login Attempt Counter

Using:

* global variable version
* nonlocal version

Students compare.

Example:

```python id="jlwmgu"
attempts = 0

def login():
    global attempts
    attempts += 1
```

Then better approach.

---

# 17. Class Assignments

## Level 1 (Basic)

1. Local variable example
2. Global variable example
3. LEGB practice

---

## Level 2 (Intermediate)

1. Student marks using scope
2. Counter increment
3. Nested function

---

## Level 3 (Placement)

1. Debug scope errors
2. Predict outputs
3. Fix `UnboundLocalError`

---

## Level 4 (Real World)

Build:

### Login Attempt Tracker

Track failed logins.

Using nested functions.

---

# Secret Concepts Students Usually Don’t Know

### 1. LEGB rule

Most students don’t know.

---

### 2. Built-in overriding bug

Very common industry issue.

---

### 3. `UnboundLocalError`

Huge placement trap.

---

### 4. Closures

Advanced Python.

---

### 5. Local variables cleaned automatically

Memory optimization.

---

### End-of-Class Assessment

Ask:

1. What is LEGB?
2. Difference between global & nonlocal?
3. Why `UnboundLocalError` occurs?
4. Why global bad?
5. What is closure?
6. Predict tricky outputs.

Next concept:

## **Error Handling (Exception Handling)** — extremely important for placements + real-world development.
