# Pre-Training Assessment Test – Set 4 (Basic Level)

**Duration:** 60 Minutes
**Total Marks:** 50

### Instructions:

* Answer all questions.
* Do not use AI tools or external help.
* Write clean and readable code.
* Follow proper input/output format.

---

# Section A: MCQs (20 Questions)

### 1. Which keyword is used to print output in Python?

A) show()
B) display()
C) print()
D) echo()

---

### 2. What will be the output?

```python id="s2p9qw"
x = 10
print(type(x))
```

A) str
B) float
C) int
D) bool

---

### 3. Which symbol is used for single-line comments in Python?

A) //
B) #
C) /* */
D) --

---

### 4. Which data type stores multiple values?

A) int
B) float
C) list
D) bool

---

### 5. What will be the output?

```python id="h7x4qp"
print(20 > 10)
```

A) True
B) False
C) Error
D) None

---

### 6. Which function is used to take input from the user?

A) scan()
B) get()
C) input()
D) read()

---

### 7. What will be the output?

```python id="l3m1zd"
x = 5
y = 2
print(x + y)
```

A) 7
B) 10
C) 3
D) Error

---

### 8. Which loop is used to repeat a block of code?

A) if
B) else
C) for
D) define

---

### 9. What will be the output?

```python id="8g4znr"
print("Python" + "Programming")
```

A) Python Programming
B) Python+Programming
C) PythonProgramming
D) Error

---

### 10. Which operator is used for multiplication?

A) +
B) *
C) %
D) /

---

### 11. Which HTML tag is used to create a heading?

A) `<head>`
B) `<h1>`
C) `<title>`
D) `<body>`

---

### 12. Which CSS property changes text size?

A) font-style
B) text-size
C) font-size
D) text-font

---

### 13. Which keyword declares a variable in JavaScript?

A) variable
B) let
C) define
D) create

---

### 14. What will be the output?

```python id="fd5v2k"
x = [1, 2, 3]
print(x[0])
```

A) 1
B) 2
C) 3
D) Error

---

### 15. Which statement is used for checking conditions?

A) loop
B) if
C) continue
D) break

---

### 16. What will be the output?

```python id="lh5v3n"
print(9 % 2)
```

A) 4
B) 1
C) 0
D) 9

---

### 17. Which keyword is used to create a function?

A) function
B) define
C) def
D) func

---

### 18. Which HTTP method is used to send data?

A) GET
B) DELETE
C) POST
D) FETCH

---

### 19. Which extension is used for Python files?

A) `.pt`
B) `.python`
C) `.py`
D) `.code`

---

### 20. What will be the output?

```python id="8rbh4m"
print(len("Hello"))
```

A) 4
B) 5
C) 6
D) Error

---

# Section B: Coding Questions (Basic Level)

## Question 1: Pattern Problem

Write a Python program to print the following pattern.

**Input:**

```text id="y91xpv"
5
```

**Output:**

```text id="u3rz4v"
1
12
123
1234
12345
```

---

## Question 2: Even or Odd

Write a Python program to check whether a given number is **Even or Odd**.

### Example Input:

```text id="7zy0x8"
8
```

### Example Output:

```text id="6n5ybj"
Even
```

---

## Question 3: Sum of Digits

Write a Python program to find the **sum of digits of a given number**.

### Example Input:

```text id="xjlwm0"
123
```

### Example Output:

```text id="m71v6q"
6
```

# Pre-Training Assessment Test – Set 4 (Answer Key)

## Section A: MCQs Answers

| Q.No | Answer                   |
| ---- | ------------------------ |
| 1    | **C) print()**           |
| 2    | **C) int**               |
| 3    | **B) #**                 |
| 4    | **C) list**              |
| 5    | **A) True**              |
| 6    | **C) input()**           |
| 7    | **A) 7**                 |
| 8    | **C) for**               |
| 9    | **C) PythonProgramming** |
| 10   | **B) ***                 |
| 11   | **B) `<h1>`**            |
| 12   | **C) font-size**         |
| 13   | **B) let**               |
| 14   | **A) 1**                 |
| 15   | **B) if**                |
| 16   | **B) 1**                 |
| 17   | **C) def**               |
| 18   | **C) POST**              |
| 19   | **C) .py**               |
| 20   | **B) 5**                 |

---

# Section B: Coding Questions – Sample Solutions

## Question 1: Pattern Problem

```python id="0ecjlwm"
n = int(input())

for i in range(1, n + 1):
    print(str(i) * i)
```

### Example Output (`n = 5`)

```text id="t5jsqq"
1
22
333
4444
55555
```

---

## Question 2: Even or Odd

### Method 1: Using Modulus Operator

```python id="74wv6m"
num = int(input())

if num % 2 == 0:
    print("Even")
else:
    print("Odd")
```

### Example Output

**Input:**

```text id="jlwmxx"
8
```

**Output:**

```text id="7p1lwp"
Even
```

---

## Question 3: Sum of Digits

### Method 1: Using While Loop

```python id="vv57v5"
num = int(input())

sum_digits = 0

while num > 0:
    digit = num % 10
    sum_digits += digit
    num //= 10

print(sum_digits)
```

### Example Output

**Input:**

```text id="wn33yv"
123
```

**Output:**

```text id="2kr34p"
6
```

### Alternative Method (Easy Approach)

```python id="jlwm0r"
num = input()

sum_digits = 0

for digit in num:
    sum_digits += int(digit)

print(sum_digits)
```

---

## Evaluation Criteria

| Score Range | Level                      |
| ----------- | -------------------------- |
| **0–15**    | Beginner                   |
| **16–30**   | Intermediate               |
| **31–40**   | Good                       |
| **41–50**   | Advanced / Placement Ready |

