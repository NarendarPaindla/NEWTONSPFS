## Concept 5: Functions (Placement-Oriented + Industry + Hidden Concepts)

### Goal of this Module

By end of class students should:

✅ Understand why functions are important
✅ Write reusable code
✅ Understand parameters & return deeply
✅ Master scope & memory behavior
✅ Solve placement coding questions
✅ Learn recursion basics
✅ Avoid hidden bugs in interviews

---

# 1. What is a Function?

### Definition

A function is:

### **A reusable block of code that performs a specific task**

Without functions:

```python id="j2r8l1"
num1 = 10
num2 = 20
print(num1 + num2)

num1 = 30
num2 = 40
print(num1 + num2)
```

Repeated code.

Bad practice.

Using function:

```python id="3ozjlwm"
def add(a, b):
    print(a + b)

add(10, 20)
add(30, 40)
```

Reusable.

Clean.

Professional.

---

# 2. Why Functions Matter in Industry?

Benefits:

### 1. Reusability

Write once, use many times.

### 2. Maintainability

Easy to fix bugs.

### 3. Readability

Cleaner code.

### 4. Testing

Easy unit testing.

### 5. Scalability

Large systems need modular code.

Interview answer.

---

# 3. Function Syntax

```python id="e2t10m"
def function_name(parameters):
    code
```

Example:

```python id="tjlwmg"
def greet():
    print("Hello Students")
```

Calling function:

```python id="3by1px"
greet()
```

---

# Hidden Concept

### Function is not executed until called

```python id="2l4bwi"
def hello():
    print("Hi")
```

No output.

Because:

Function only defined.

Not called.

Most beginners misunderstand this.

---

# 4. Types of Functions

### 1. Built-in Functions

Python already provides.

Examples:

```python id="ohij1n"
print()
len()
sum()
max()
min()
type()
```

---

### 2. User-defined Functions

Created by developer.

Example:

```python id="d9kwdf"
def square(num):
    return num * num
```

---

# 5. Parameters vs Arguments

Most asked interview question.

### Parameter

Variable in function definition.

```python id="kll5mz"
def greet(name):
```

`name`

Parameter.

---

### Argument

Actual value passed.

```python id="owb5wy"
greet("Narendar")
```

"Narendar"

Argument.

---

### Interview Answer

Parameter → Placeholder

Argument → Actual value

---

# 6. Function Types

---

## Type 1: No Parameter, No Return

```python id="xjlwmg"
def greet():
    print("Hello")
```

---

## Type 2: Parameter, No Return

```python id="mjlwmg"
def greet(name):
    print("Hello", name)
```

---

## Type 3: No Parameter, Return

```python id="jlwmgc"
def get_pi():
    return 3.14
```

---

## Type 4: Parameter + Return (Most Important)

```python id="glk9wm"
def add(a, b):
    return a + b
```

Industry standard.

---

# 7. Return vs Print (Very Important)

### Wrong Understanding

Students think both same.

Not same.

---

### print()

Displays value.

```python id="mdjwue"
def add(a, b):
    print(a + b)
```

---

### return

Sends value back.

```python id="jlwmgh"
def add(a, b):
    return a + b
```

Can reuse:

```python id="jlwmg9"
result = add(10, 20)

print(result * 2)
```

---

### Hidden Interview Question

Output?

```python id="cjlwmg"
def add():
    print(10)

x = add()

print(x)
```

Answer:

```python id="jlwmgf"
10
None
```

Why?

No return.

Default:

```python id="jlwmga"
return None
```

Very important.

---

# 8. Default Parameters

Example:

```python id="jlwmgb"
def greet(name="Student"):
    print(name)
```

Call:

```python id="jlwmgd"
greet()
```

Output:

```python id="jlwmge"
Student
```

---

### Hidden Trick

Bad Practice:

```python id="jlwmgx"
def add(a=[]):
    a.append(1)
    return a
```

Output:

```python id="jlwmgq"
print(add())
print(add())
```

Result:

```python id="jlwmgw"
[1]
[1,1]
```

Students shocked.

### Why?

Mutable default parameter bug.

Interview killer question.

Correct:

```python id="jlwmgr"
def add(a=None):
    if a is None:
        a = []

    a.append(1)

    return a
```

Industry best practice.

---

# 9. Keyword Arguments

Example:

```python id="jlwmgt"
def student(name, age):
    print(name, age)

student(age=22, name="Narendar")
```

Order not required.

---

# 10. Positional Arguments

Order matters.

```python id="jlwmgu"
student("Narendar", 22)
```

---

# Hidden Interview Question

Output?

```python id="jlwmgv"
def demo(a, b):
    print(a, b)

demo(b=10, a=20)
```

Answer:

```python id="jlwmgy"
20 10
```

---

# 11. Variable-Length Arguments

---

## *args

Multiple positional inputs.

Example:

```python id="jlwmgz"
def total(*numbers):
    print(sum(numbers))

total(10, 20, 30)
```

---

### Hidden Concept

Type of args?

Answer:

```python id="jlwmg0"
tuple
```

Interview favorite.

---

## **kwargs

Multiple keyword inputs.

Example:

```python id="jlwmg1"
def profile(**data):
    print(data)

profile(name="Narendar", age=24)
```

Type?

Answer:

```python id="jlwmg2"
dictionary
```

---

# 12. Scope (Very Important)

---

## Local Scope

Inside function.

```python id="jlwmg3"
def test():
    x = 10
```

Cannot access outside.

---

## Global Scope

Outside function.

```python id="jlwmg4"
x = 100
```

---

### Hidden Trap

Output?

```python id="jlwmg5"
x = 10

def test():
    print(x)

test()
```

Output:

```python id="jlwmg6"
10
```

---

### Hidden Trap 2

```python id="jlwmg7"
x = 10

def test():
    x = 20

test()

print(x)
```

Output:

```python id="jlwmg8"
10
```

Different scope.

---

### global Keyword

Example:

```python id="jlwmg9"
x = 10

def test():
    global x
    x = 50

test()

print(x)
```

Output:

```python id="jlwmga0"
50
```

Teach:

Avoid global variables.

Industry bad practice.

---

# 13. Recursion (Placement Important)

Function calling itself.

Example:

Factorial:

```python id="jlwmga1"
def factorial(n):

    if n == 1:
        return 1

    return n * factorial(n - 1)

print(factorial(5))
```

Output:

```python id="jlwmga2"
120
```

---

### Hidden Concept

### Base Condition mandatory

Else:

### Stack Overflow

```python id="jlwmga3"
def infinite():
    infinite()
```

Crash.

---

### Interview Question

Recursion vs Loop?

### Answer

Loop:

Memory efficient.

Recursion:

Cleaner for tree/graph problems.

---

# 14. Lambda Functions

Small one-line functions.

Example:

```python id="jlwmga4"
square = lambda x: x*x
```

Usage:

```python id="jlwmga5"
print(square(5))
```

Output:

```python id="jlwmga6"
25
```

Industry:

Sorting.

Transformations.

---

# 15. Hidden Concepts (Very Important)

---

## Function is Object

```python id="jlwmga7"
def hello():
    print("Hi")

x = hello

x()
```

Output:

```python id="jlwmga8"
Hi
```

Most students don’t know.

---

## Multiple Return Values

```python id="jlwmga9"
def calc(a, b):
    return a+b, a-b
```

Output:

```python id="jlwmgb0"
x, y = calc(10, 5)
```

Actually:

Tuple packing/unpacking.

---

## Function Memory Reference

```python id="jlwmgb1"
def modify(lst):
    lst.append(100)

nums = [1,2]

modify(nums)

print(nums)
```

Output:

```python id="jlwmgb2"
[1,2,100]
```

Why?

Pass by object reference.

Huge interview question.

---

# 16. Tricky Placement Questions

### Question 1

Output?

```python id="jlwmgb3"
def test():
    pass

print(test())
```

Answer:

```python id="jlwmgb4"
None
```

---

### Question 2

```python id="jlwmgb5"
def add(a=[]):
    a.append(1)
    return a

print(add())
print(add())
```

Answer:

```python id="jlwmgb6"
[1]
[1,1]
```

---

### Question 3

```python id="jlwmgb7"
def demo(x):
    x = 50

a = 10

demo(a)

print(a)
```

Answer:

```python id="jlwmgb8"
10
```

---

### Question 4

```python id="jlwmgb9"
def hello():
    return

print(hello())
```

Answer:

```python id="jlwmgc0"
None
```

---

### Question 5

```python id="jlwmgc1"
def calc(a,b):
    return a+b, a*b

print(calc(2,3))
```

Answer:

```python id="jlwmgc2"
(5,6)
```

---

# 17. Placement Interview Questions + Answers

### Q1: Difference between parameter and argument?

Parameter → placeholder
Argument → actual value.

---

### Q2: Difference between return and print?

print → display

return → send value back.

---

### Q3: What happens if no return statement?

Python returns:

```python id="jlwmgc3"
None
```

---

### Q4: Why mutable default arguments dangerous?

Shared memory issue.

Unexpected behavior.

---

### Q5: What is recursion?

Function calling itself.

Needs base condition.

---

### Q6: What is *args and **kwargs?

`*args`

Variable positional arguments.

Tuple.

`**kwargs`

Variable keyword arguments.

Dictionary.

---

# 18. Live Practical Activity

### Student Result Function System

Functions:

* calculate_total()
* calculate_average()
* calculate_grade()
* display_result()

Students learn modular coding.

---

# 19. Class Assignments

## Level 1 (Basic)

1. Add two numbers
2. Even/Odd function
3. Maximum of two numbers

---

## Level 2 (Intermediate)

1. Calculator
2. Prime checker
3. Fibonacci

---

## Level 3 (Placement)

1. Recursion factorial
2. Palindrome function
3. Armstrong function
4. Pattern generator

---

## Level 4 (Real World)

Build:

### Banking Menu System

Using functions:

* deposit()
* withdraw()
* check_balance()

---

# Secret Concepts Students Usually Don’t Know

### 1. Mutable default argument bug

Interview killer.

---

### 2. Function is object

Advanced Python.

---

### 3. return None default

Very important.

---

### 4. Multiple return values = tuple

Most don’t know.

---

### 5. Python uses object references

Not pass by reference/value exactly.

Huge interview question.

### End-of-Class Assessment

Ask:

1. Difference between return & print?
2. Why mutable default dangerous?
3. What is recursion?
4. What are *args & **kwargs?
5. Why global variable bad?
6. Predict tricky outputs.

Next concept:

## **Scope + Error Handling** (deep placement-oriented).
