Perfect. Since students are **absolute beginners**, you should teach **Loops** in a way that:

> **“They understand WHY loops exist before HOW loops work.”**

If you directly start:

```python
for i in range(5):
```

students will get confused.

For weak students, teach:

## Real Life → Dry Run → Visualization → Syntax → Practice → Logic Building

By the end, students should confidently solve:

✅ Printing problems
✅ Tables
✅ Sum logic
✅ Count digits
✅ Pattern problems
✅ Basic placement loop questions

---

# DAY-4 CLASS DESIGN

# LOOPS IN PYTHON

### Duration: 5–6 Hours

### Final Outcome

Students should understand:

1. Why loops needed
2. `for` loop
3. `range()`
4. `while` loop
5. `break`
6. `continue`
7. Nested loops
8. Basic pattern problems
9. Logic-building using loops

---

# PHASE 1 — Curiosity Hook (15 mins)

## DON'T START WITH SYNTAX

Ask students:

### Question:

Print:

```text
Hello
```

10 times.

Student writes:

```python
print("Hello")
print("Hello")
print("Hello")
```

Ask:

### What if company asks 10,000 times?

Students laugh 😄

Then introduce:

### Loop = Repeat work automatically.

Memory sentence:

## Loop means repetition.

---

# PHASE 2 — What is Loop? (20 mins)

### Definition

Loop is:

> **Repeating same task multiple times automatically**

---

## Real Life Examples

### Example 1: Fan

Fan rotates continuously.

Loop.

---

### Example 2: Attendance

Teacher checking 100 students.

Repetition.

Loop.

---

### Example 3: Instagram Scroll

Videos repeat.

Loop.

Students relate immediately.

---

# PHASE 3 — for Loop (Most Important)

Tell:

### Use `for loop`

When repetitions are known.

Example:

Print hello 5 times.

---

## Syntax

```python
for variable in range(number):
    statement
```

---

### First Program

```python
for i in range(5):
    print("Hello")
```

Output:

```text
Hello
Hello
Hello
Hello
Hello
```

---

## Explain Slowly

### What is `i`?

Counter variable.

Like:

```text
1st time
2nd time
3rd time
```

---

## Dry Run Table (VERY IMPORTANT)

Code:

```python
for i in range(5):
    print(i)
```

| i Value | Output |
| ------- | ------ |
| 0       | 0      |
| 1       | 1      |
| 2       | 2      |
| 3       | 3      |
| 4       | 4      |

Teach visually.

Students remember longer.

---

# PHASE 4 — Understanding range() (40 mins)

Most confusing for students.

Teach slowly.

---

## Type 1

```python
range(5)
```

Meaning:

```text
0 to 4
```

Memory Trick:

### Ending number excluded

---

## Type 2

```python
range(1, 6)
```

Output:

```text
1 2 3 4 5
```

Meaning:

```text
start, stop
```

---

## Type 3

```python
range(1, 10, 2)
```

Output:

```text
1 3 5 7 9
```

Meaning:

```text
start, stop, jump
```

Call:

### Step value.

---

## Reverse Counting

```python
for i in range(10, 0, -1):
    print(i)
```

Output:

```text
10
9
8
7
...
1
```

Students enjoy reverse loops.

---

# PHASE 5 — Practice Coding (Easy Level)

## Task 1

Print numbers 1–10

```python
for i in range(1,11):
    print(i)
```

---

## Task 2

Print even numbers

Output:

```text
2 4 6 8 10
```

---

## Task 3

Print odd numbers

---

## Task 4

Print student name 5 times

---

## Task 5

Countdown Timer

Output:

```text
5
4
3
2
1
Boom!
```

Fun task 😄

---

# PHASE 6 — Logic Building (Medium)

## Multiplication Table

Input:

```text
5
```

Output:

```text
5 x 1 = 5
5 x 2 = 10
```

Code:

```python
num = int(input())

for i in range(1,11):
    print(num, "x", i, "=", num*i)
```

---

## Sum of First N Numbers

Input:

```text
5
```

Output:

```text
15
```

Logic:

```text
1+2+3+4+5
```

---

## Factorial Intro

Input:

```text
5
```

Output:

```text
120
```

Teach slowly.

---

# PHASE 7 — while Loop (40 mins)

Tell:

### When repetitions unknown

Use:

### while loop

---

## Syntax

```python
while condition:
    statement
```

---

### Example

```python
count = 1

while count <= 5:
    print(count)

    count += 1
```

---

## Dry Run Table

| count | condition | output |
| ----- | --------- | ------ |
| 1     | True      | 1      |
| 2     | True      | 2      |
| 3     | True      | 3      |
| 4     | True      | 4      |
| 5     | True      | 5      |
| 6     | False     | stop   |

Students understand visually.

---

# Biggest Mistake

## Infinite Loop

Wrong:

```python
count = 1

while count <= 5:
    print(count)
```

Ask:

### Why not stopping?

Because:

No increment.

Students remember mistakes longer.

---

# PHASE 8 — Fun While Loop Tasks

### Password Retry System

Correct password:

```text
python123
```

---

### Guess the Number Game 😄

Secret number:

```text
7
```

Student guesses.

---

### Countdown Timer

---

# PHASE 9 — break Statement (20 mins)

Definition:

### Immediately stop loop

Example:

```python
for i in range(10):

    if i == 5:
        break

    print(i)
```

Output:

```text
0
1
2
3
4
```

---

## Real Life Example

Exit game.

Stop ATM transaction.

---

# PHASE 10 — continue Statement

Definition:

### Skip current iteration

Example:

```python
for i in range(5):

    if i == 2:
        continue

    print(i)
```

Output:

```text
0
1
3
4
```

---

# PHASE 11 — Nested Loops (Simple Intro)

Only basics.

Example:

```python
for i in range(3):

    for j in range(3):

        print(i, j)
```

Explain:

Outer loop × inner loop.

---

# PHASE 12 — Pattern Problems (FUN 😄)

### Pattern 1

```text
*
**
***
****
```

Code:

```python
for i in range(1,5):
    print("*" * i)
```

Students enjoy patterns.

---

### Pattern 2

```text
1
12
123
1234
```

---

### Pattern 3

Reverse stars

```text
****
***
**
*
```

---

# PHASE 13 — Logic Ladder

## Level 1 (Easy)

1. Print 1–10
2. Even numbers
3. Odd numbers
4. Reverse counting
5. Name 5 times

---

## Level 2 (Medium)

1. Multiplication table
2. Sum of N numbers
3. Factorial
4. Count digits
5. Reverse number

---

## Level 3 (Interesting)

1. Guess game
2. Password retry
3. Countdown timer
4. ATM attempts

---

## Level 4 (Placement Intro)

1. Prime number
2. Fibonacci
3. Armstrong number
4. Palindrome number

(Do later, not same day.)

---

# Must Teach Hidden Concepts

### 1.

```python
range(5)
```

Means:

```text
0 to 4
```

NOT 1–5.

Huge confusion.

---

### 2.

Loop variable changes automatically.

---

### 3.

Infinite loops.

Very important.

---

### 4.

Difference:

`for`

Known repetitions.

`while`

Unknown repetitions.

---

# End Class Assessment

Ask students:

### Write without seeing notes:

1. for syntax
2. while syntax
3. range syntax
4. break
5. continue

Then:

### 10-minute coding challenge

Small loop problems.

This makes them remember for long time.

**After this class**, students should be comfortable enough to start **loop logic-building problems** (prime, palindrome, reverse number, digit extraction, patterns).
