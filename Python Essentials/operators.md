## Concept 3: Operators (Placement-Oriented + Interview + Hidden Tricks)

### Goal of this Module

By end of this class students should:

✅ Understand all operator types
✅ Solve placement coding questions
✅ Predict tricky outputs
✅ Understand precedence & short-circuiting
✅ Avoid logical bugs in interviews
✅ Debug operator mistakes

---

# 1. What are Operators?

### Definition

Operators are **symbols that perform operations on values or variables**.

Example:

```python
a = 10
b = 20

print(a + b)
```

Here:

`+` → Operator

`a` and `b` → Operands

---

# 2. Types of Operators in Python

1. Arithmetic Operators
2. Assignment Operators
3. Comparison Operators
4. Logical Operators
5. Identity Operators
6. Membership Operators
7. Bitwise Operators (Important for placements)

---

# 3. Arithmetic Operators

Used for mathematical operations.

| Operator | Meaning        |
| -------- | -------------- |
| `+`      | Addition       |
| `-`      | Subtraction    |
| `*`      | Multiplication |
| `/`      | Division       |
| `//`     | Floor Division |
| `%`      | Modulus        |
| `**`     | Power          |

Example:

```python
a = 10
b = 3

print(a + b)
print(a - b)
print(a * b)
print(a / b)
print(a // b)
print(a % b)
print(a ** b)
```

Output:

```python
13
7
30
3.3333
3
1
1000
```

---

## Hidden Concept: `/` vs `//`

### Normal Division

```python
print(10 / 3)
```

Output:

```python
3.333333
```

### Floor Division

```python
print(10 // 3)
```

Output:

```python
3
```

### Hidden Trick

Negative numbers:

```python
print(-10 // 3)
```

Output:

```python
-4
```

Most students say `-3`.

### Why?

Floor division rounds **towards negative infinity**, not zero.

Very important interview trap.

---

## Modulus Operator (`%`)

Finds remainder.

```python
print(10 % 3)
```

Output:

```python
1
```

### Real-world Uses

* Even/Odd
* Circular indexing
* Password validation
* Clock systems

Example:

```python
num = 8

if num % 2 == 0:
    print("Even")
```

---

## Exponent (`**`)

Power operator.

```python
print(2 ** 4)
```

Output:

```python
16
```

Used in:

* ML formulas
* Scientific computing
* Finance

---

# 4. Assignment Operators

| Operator | Meaning         |
| -------- | --------------- |
| `=`      | Assign          |
| `+=`     | Add assign      |
| `-=`     | Subtract assign |
| `*=`     | Multiply assign |
| `/=`     | Divide assign   |

Example:

```python
x = 10

x += 5

print(x)
```

Output:

```python
15
```

Equivalent:

```python
x = x + 5
```

---

## Hidden Concept: Mutable Object Trap

### Question

Output?

```python
a = [1, 2]
b = a

b += [3]

print(a)
```

Answer:

```python
[1,2,3]
```

Why?

For mutable objects:

`+=` modifies original object.

Most students fail this.

---

# 5. Comparison Operators

Used for comparison.

| Operator | Meaning       |
| -------- | ------------- |
| `==`     | Equal         |
| `!=`     | Not Equal     |
| `>`      | Greater       |
| `<`      | Less          |
| `>=`     | Greater Equal |
| `<=`     | Less Equal    |

Example:

```python
age = 24

print(age > 18)
```

Output:

```python
True
```

---

## Hidden Concept: `==` vs `is`

Most asked interview question.

### `==`

Checks value.

```python
a = [1,2]
b = [1,2]

print(a == b)
```

Output:

```python
True
```

---

### `is`

Checks memory location.

```python
print(a is b)
```

Output:

```python
False
```

### Memory Understanding

Different objects:

Same values ≠ Same memory.

---

## Hidden Trick Question

```python
x = None

print(x == None)
print(x is None)
```

Best practice?

### Answer

Use:

```python
x is None
```

Why?

Faster and safer.

Interview favorite.

---

# 6. Logical Operators

Used for conditions.

| Operator | Meaning           |
| -------- | ----------------- |
| `and`    | Both True         |
| `or`     | At least one True |
| `not`    | Reverse           |

---

### AND

```python
age = 25
salary = 50000

print(age > 18 and salary > 30000)
```

Output:

```python
True
```

---

### OR

```python
print(True or False)
```

Output:

```python
True
```

---

### NOT

```python
print(not True)
```

Output:

```python
False
```

---

# 7. Hidden Concept: Short Circuiting (Very Important)

### AND stops early

```python
print(False and print("Hello"))
```

Output:

```python
False
```

"Hello" not printed.

Why?

First value already False.

No need to check next.

---

### OR stops early

```python
print(True or print("Hello"))
```

Output:

```python
True
```

Interview-level concept.

### Real-world Use

Avoid crashes:

```python
name = None

if name and len(name) > 3:
    print("Valid")
```

Why useful?

Prevents:

```python
TypeError
```

---

# 8. Membership Operators

Check presence.

| Operator | Meaning       |
| -------- | ------------- |
| `in`     | Exists        |
| `not in` | Doesn't exist |

Example:

```python
skills = ["Python", "Java"]

print("Python" in skills)
```

Output:

```python
True
```

### Real-world Use

Login validation

Permission systems

Search filtering

---

## Hidden Trick

```python
print("P" in "Python")
```

Output:

```python
True
```

Works on strings too.

---

# 9. Identity Operators

| Operator | Meaning          |
| -------- | ---------------- |
| `is`     | Same object      |
| `is not` | Different object |

Example:

```python
a = [1,2]
b = a

print(a is b)
```

Output:

```python
True
```

Same memory.

---

### Hidden Trick: Small Integer Caching

```python
a = 256
b = 256

print(a is b)
```

Often:

```python
True
```

But:

```python
a = 1000
b = 1000

print(a is b)
```

Can vary.

### Why?

Python caches small integers.

Range usually:

```python
-5 to 256
```

Advanced interview question.

---

# 10. Bitwise Operators (Placement Important)

Usually asked in product companies.

| Operator | Meaning     |    |
| -------- | ----------- | -- |
| `&`      | AND         |    |
| `        | `           | OR |
| `^`      | XOR         |    |
| `~`      | NOT         |    |
| `<<`     | Left Shift  |    |
| `>>`     | Right Shift |    |

Example:

```python
print(5 & 3)
```

Binary:

```text
5 → 101
3 → 011
---------
    001
```

Output:

```python
1
```

---

### XOR Trick

Very important placement question.

Swap without temp variable:

```python
a = 10
b = 20

a = a ^ b
b = a ^ b
a = a ^ b
```

---

# 11. Operator Precedence (Most Important)

Question:

Predict output:

```python
print(10 + 2 * 5)
```

Output:

```python
20
```

Why?

Multiplication first.

Rule:

### PEMDAS/BODMAS

Parentheses first.

---

### Hidden Trap

```python
print(2 ** 3 ** 2)
```

Output?

Answer:

```python
512
```

Because:

Right to left.

```text
2^(3^2)

2^9
```

Most students say 64.

---

# 12. Tricky Placement Questions

### Q1

Output?

```python
print(10 == 10.0)
```

Answer:

```python
True
```

Why?

Values same.

---

### Q2

```python
print(bool([]))
```

Answer:

```python
False
```

Falsy object.

---

### Q3

```python
print(5 > 3 > 1)
```

Answer:

```python
True
```

Python chaining.

Equivalent:

```python
5 > 3 and 3 > 1
```

Rare interview question.

---

### Q4

```python
print(10 or 20)
```

Answer:

```python
10
```

Why?

Python returns first truthy value.

---

### Q5

```python
print("" or "Python")
```

Answer:

```python
Python
```

Very hidden concept.

---

### Q6

```python
print([] and "Hello")
```

Answer:

```python
[]
```

Why?

AND returns first falsy value.

---

# 13. Interview Questions + Answers

### Q1: Difference between `==` and `is`?

### Answer

`==` compares values.

`is` compares memory location.

---

### Q2: Why `is None` preferred?

### Answer

Safer, optimized, identity comparison.

---

### Q3: What is short circuiting?

### Answer

Logical operators stop evaluating once result is known.

Improves performance.

Avoids errors.

---

### Q4: Difference between `/` and `//`?

### Answer

`/` → Decimal division

`//` → Floor division

---

### Q5: Why XOR useful?

### Answer

Bit manipulation.

Memory optimization.

Efficient toggling/swapping.

---

# 14. Live Practical Activity

### Student Eligibility Checker

Input:

* Age
* CGPA
* Backlogs

Eligibility:

```python
if age >= 18 and cgpa >= 7 and backlogs == 0:
    print("Eligible")
else:
    print("Not Eligible")
```

---

# 15. Class Assignments

## Level 1 (Basic)

1. Calculator using operators
2. Even/odd checker
3. Largest number

---

## Level 2 (Intermediate)

1. Salary bonus calculator
2. Discount calculator
3. Grade evaluator

---

## Level 3 (Placement)

Predict outputs.

Debug operator mistakes.

Operator precedence questions.

---

## Level 4 (Real World)

Build:

### Loan Eligibility System

Conditions:

* Salary
* Age
* Credit score
* Existing loan

---

# 16. Secret Concepts Students Rarely Know

### 1. Logical operators return values

Not boolean always.

```python
print(10 and 20)
```

Output:

```python
20
```

Why?

Returns last truthy value.

---

### 2. Chained comparison

```python
print(1 < 5 < 10)
```

Valid Python syntax.

---

### 3. Negative floor division trap

```python
print(-7 // 2)
```

Output:

```python
-4
```

Huge interview trap.

---

### End-of-Class Assessment

Ask students:

1. Difference between `==` and `is`
2. What is short circuiting?
3. Explain floor division.
4. Why XOR useful?
5. Predict precedence outputs.
6. Why `10 or 20` returns 10?

Next concept:

## **Input/Output + Type Casting** (important before Control Flow)
