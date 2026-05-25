## Concept 4: Control Flow (Placement-Oriented + Industry + Hidden Tricks)

### Goal of this Module

By end of class students should:

✅ Understand decision making in Python
✅ Write conditions confidently
✅ Master loops & logic building
✅ Solve placement coding questions
✅ Avoid infinite loops
✅ Understand loop internals
✅ Debug common mistakes

---

# 1. What is Control Flow?

### Definition

Control Flow decides:

### **How program executes step by step**

Without control flow:

Program runs line by line.

Example:

```python
print("Step 1")
print("Step 2")
print("Step 3")
```

But real-world applications need:

### Decision Making

Example:

ATM:

* If balance available → withdraw
* Else → insufficient balance

This is:

### Control Flow

---

# 2. Types of Control Flow

1. Conditional Statements
2. Loops
3. Loop Control Statements
4. Nested Control Flow
5. Pattern Logic (Placement Important)

---

# PART 1: CONDITIONAL STATEMENTS

---

# 3. if Statement

Used when condition is True.

Syntax:

```python
if condition:
    statement
```

Example:

```python
age = 20

if age >= 18:
    print("Eligible to vote")
```

---

## Hidden Concept

Python checks:

### Truthy & Falsy values

Falsy:

```python
0
0.0
[]
{}
()
""
False
None
```

Everything else:

Truthy

Example:

```python
name = ""

if name:
    print("Valid")
else:
    print("Invalid")
```

Output:

```python
Invalid
```

Interview favorite.

---

# 4. if-else Statement

Used for decision making.

Example:

```python
marks = 45

if marks >= 40:
    print("Pass")
else:
    print("Fail")
```

---

### Real Industry Example

Login system:

```python
password = "admin123"

if password == "admin123":
    print("Login Success")
else:
    print("Wrong Password")
```

---

# 5. if-elif-else Ladder

Used for multiple conditions.

Example:

```python
marks = 85

if marks >= 90:
    print("A Grade")

elif marks >= 75:
    print("B Grade")

elif marks >= 50:
    print("C Grade")

else:
    print("Fail")
```

---

## Hidden Interview Concept

### Order Matters

Wrong:

```python
marks = 95

if marks >= 50:
    print("Pass")

elif marks >= 90:
    print("Topper")
```

Output:

```python
Pass
```

Why?

Python stops after first True.

Huge logical mistake students make.

---

# 6. Nested If

Condition inside condition.

Example:

```python
age = 25
citizen = True

if age >= 18:
    if citizen:
        print("Eligible")
```

---

### Real-world Use

Loan approval

Job eligibility

Access control

---

# Hidden Concept

Avoid too much nesting.

Bad code:

```python
if age > 18:
    if marks > 70:
        if fees_paid:
            if attendance > 75:
                print("Eligible")
```

Teach:

### Clean coding approach

Better:

```python
if (
    age > 18 and
    marks > 70 and
    fees_paid and
    attendance > 75
):
    print("Eligible")
```

Industry practice.

---

# 7. Match Case (Python 3.10+)

Very important modern Python.

Alternative to switch case.

Example:

```python
day = 2

match day:
    case 1:
        print("Monday")

    case 2:
        print("Tuesday")

    case _:
        print("Invalid")
```

### Interview Question

Does Python have switch case?

### Answer

Before Python 3.10 → No

Now:

### `match-case`

---

# PART 2: LOOPS

---

# 8. Why Loops?

Without loop:

```python
print("Hello")
print("Hello")
print("Hello")
```

With loop:

```python
for i in range(3):
    print("Hello")
```

Less code.

Reusable.

---

# 9. for Loop

Used when iterations known.

Syntax:

```python
for variable in iterable:
    code
```

Example:

```python
for i in range(5):
    print(i)
```

Output:

```python
0
1
2
3
4
```

---

# Hidden Concept

### `range()` does not create list

Most students don't know.

It creates:

### Range Object (lazy)

Efficient memory usage.

Check:

```python
print(type(range(5)))
```

Output:

```python
<class 'range'>
```

Interview point.

---

# 10. range() Variations

### Single Parameter

```python
range(5)
```

0 → 4

---

### Start, End

```python
range(2, 8)
```

2 → 7

---

### Step

```python
range(1, 10, 2)
```

Odd numbers.

---

### Reverse Loop

```python
for i in range(10, 0, -1):
    print(i)
```

Very important.

---

# Hidden Trap

Output?

```python
for i in range(5):
    i += 1

print(i)
```

Answer:

```python
4
```

Most students say 5.

Because loop variable resets.

---

# 11. while Loop

Used when iterations unknown.

Syntax:

```python
while condition:
    code
```

Example:

```python
count = 1

while count <= 5:
    print(count)
    count += 1
```

---

### Real-world Example

Password retry:

```python
attempts = 3

while attempts > 0:
    pwd = input("Enter password: ")

    if pwd == "admin":
        print("Success")
        break

    attempts -= 1
```

Industry relevant.

---

# Hidden Danger

### Infinite Loop

```python
while True:
    print("Hello")
```

Never stops.

Fix:

Need break condition.

---

# 12. break Statement

Stops loop immediately.

Example:

```python
for i in range(10):

    if i == 5:
        break

    print(i)
```

Output:

```python
0
1
2
3
4
```

---

# 13. continue Statement

Skips iteration.

Example:

```python
for i in range(5):

    if i == 2:
        continue

    print(i)
```

Output:

```python
0
1
3
4
```

---

# Hidden Trap

Output?

```python
for i in range(5):
    if i == 2:
        continue

    print(i)
```

Students often say infinite loop.

Only while loop can cause infinite loop usually.

---

# 14. pass Statement

Placeholder.

Example:

```python
if True:
    pass
```

Used when logic not written yet.

Industry use:

During development.

---

# 15. Loop Else (Very Hidden Concept)

Most students never know this.

Python special feature.

Example:

```python
for i in range(5):
    print(i)

else:
    print("Completed")
```

Runs after loop ends.

---

### Hidden Trick

With break:

```python
for i in range(5):

    if i == 3:
        break

else:
    print("Completed")
```

Output:

Nothing.

Why?

Else runs only if loop completes naturally.

Very advanced interview question.

---

# 16. Nested Loops

Loop inside loop.

Example:

```python
for i in range(3):
    for j in range(3):
        print(i, j)
```

---

### Time Complexity Intro

Nested loops:

Usually:

### O(n²)

Placement important.

---

# 17. Pattern Problems (Must for Placements)

### Pattern 1

```text
*
**
***
****
```

Code:

```python
for i in range(1, 5):
    print("*" * i)
```

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

Reverse triangle.

---

### Pattern 4

Pyramid.

Important for logic building.

---

# 18. Tricky Placement Questions

### Question 1

Output?

```python
for i in range(5):
    print(i)

print(i)
```

Answer:

```python
4
```

Loop variable exists outside loop.

Unlike Java.

---

### Question 2

Output?

```python
for i in []:
    print("Hello")

else:
    print("Done")
```

Answer:

```python
Done
```

Rare question.

---

### Question 3

Output?

```python
x = 0

while x < 5:
    x += 1

    if x == 3:
        continue

    print(x)
```

Answer:

```python
1
2
4
5
```

---

### Question 4

Output?

```python
for i in range(3):
    print(i)
else:
    print("Finished")
```

Output:

```python
0
1
2
Finished
```

---

### Question 5

Output?

```python
for i in range(5):
    break
else:
    print("Done")
```

Output:

Nothing.

---

# 19. Placement Interview Questions + Answers

### Q1: Difference between for and while?

### Answer

| for               | while                 |
| ----------------- | --------------------- |
| Known iterations  | Unknown iterations    |
| Safer             | Risk of infinite loop |
| Used for iterable | Used for conditions   |

---

### Q2: What is infinite loop?

### Answer

Loop that never ends due to missing stopping condition.

---

### Q3: Difference between break and continue?

### Answer

`break`

Stops loop.

`continue`

Skips current iteration.

---

### Q4: What is loop else?

### Answer

Executes only when loop finishes naturally without break.

Advanced Python feature.

---

### Q5: Why range memory efficient?

### Answer

Because:

It generates values lazily.

Does not store all numbers.

Uses iterator-like behavior.

---

### Q6: What is time complexity of nested loop?

### Answer

Usually:

### O(n²)

---

# 20. Live Practical Activity

### ATM System

Menu:

```text
1. Withdraw
2. Deposit
3. Balance
4. Exit
```

Use:

* if-elif
* while loop
* break

Mini project.

---

# 21. Class Assignments

## Level 1 (Basic)

1. Even/Odd
2. Voting eligibility
3. Multiplication table

---

## Level 2 (Intermediate)

1. Prime number checker
2. Palindrome number
3. Factorial

---

## Level 3 (Placement)

1. Fibonacci
2. Armstrong number
3. Strong number
4. Perfect number
5. Pattern problems

---

## Level 4 (Real World)

Build:

### Student Result Management System

Features:

* Marks input
* Grade calculation
* Pass/Fail
* Retry option using loop

---

# Secret Concepts Students Usually Don’t Know

### 1. Loop Else

Rarely taught.

---

### 2. range() is lazy

Memory optimization.

---

### 3. Python loop variable survives

```python
for i in range(5):
    pass

print(i)
```

Output:

```python
4
```

Unlike Java/C++.

---

### 4. Truthy/Falsy behavior

Very important for writing clean code.

---

### 5. Chained Conditions

```python
print(1 < x < 10)
```

Valid Python.

Cleaner than:

```python
x > 1 and x < 10
```

---

### End-of-Class Assessment

Ask students:

1. Difference between for and while?
2. What is loop else?
3. Why infinite loop happens?
4. Difference between break & continue?
5. Why range efficient?
6. Predict tricky outputs.

Next concept:

## **Functions** (very important for placements + recursion + hidden Python behavior)


# Types of Loops Available in Python & Difference Between Them

In Python, mainly **2 types of loops** are available:

1. **for loop**
2. **while loop**

---

# 1. for Loop

### Definition

Used when:

### **Number of repetitions is known**

---

## Syntax

```python
for variable in range(number):
    statement
```

---

## Example

Print numbers from 1 to 5

```python
for i in range(1, 6):
    print(i)
```

### Output

```text
1
2
3
4
5
```

---

## Real-Life Example

### Attendance

Teacher knows:

```text
50 students
```

Repetition already known.

Use:

### for loop

---

## Common Use Cases

* Printing numbers
* Multiplication tables
* Traversing strings
* Traversing lists
* Pattern printing

---

# 2. while Loop

### Definition

Used when:

### **Number of repetitions is NOT known**

Loop runs until condition becomes False.

---

## Syntax

```python
while condition:
    statement
```

---

## Example

Print numbers from 1 to 5

```python
count = 1

while count <= 5:
    print(count)

    count += 1
```

### Output

```text
1
2
3
4
5
```

---

## Real-Life Example

### Password Login

You don't know:

### How many attempts user will take.

User may login in:

```text
1 try
3 tries
10 tries
```

Use:

### while loop

---

## Common Use Cases

* Login systems
* ATM systems
* Games
* Menu-driven programs
* Retry mechanisms

---

# Difference Between for Loop and while Loop

| Feature               | for loop         | while loop                 |
| --------------------- | ---------------- | -------------------------- |
| Repetitions           | Known            | Unknown                    |
| Condition             | Automatic        | Manual                     |
| Risk of Infinite Loop | Low              | High                       |
| Syntax                | Easier           | Slightly harder            |
| Best For              | Fixed iterations | Condition-based repetition |

---

## Example Comparison

### for loop

```python
for i in range(5):
    print(i)
```

Python automatically changes:

```python
i
```

No manual update needed.

---

### while loop

```python
i = 0

while i < 5:
    print(i)

    i += 1
```

You must manually update:

```python
i += 1
```

Otherwise:

### Infinite Loop

---

# Hidden Interview Question

### Can every for loop be converted into while loop?

### Answer

✅ Yes

Example:

### for loop

```python
for i in range(5):
    print(i)
```

Equivalent while loop:

```python
i = 0

while i < 5:
    print(i)

    i += 1
```

---

# Hidden Trick Question

### Which loop is faster?

### Answer

Usually:

### `for loop`

Why?

Python internally optimizes it better.

But difference is very small.

---

# Important Memory Trick for Students

### Use:

## **FOR = FIXED**

Known repetitions

---

## **WHILE = WAIT**

Unknown repetitions

Example:

```text
Wait until password correct
```

→ while loop

---

# Placement Interview Question

### Q: Which loop is better?

### Answer

Depends on problem.

If repetitions known:

```text
for loop
```

If repetitions unknown:

```text
while loop
```

No loop is universally better.

---

# Simple Example for Students

### for loop

Print:

```text
Hello
```

5 times.

```python
for i in range(5):
    print("Hello")
```

---

### while loop

Print until user enters:

```text
stop
```

```python
text = ""

while text != "stop":
    text = input("Enter text: ")
```

---

# Fun Classroom Activity 😄

Ask students:

### Which loop should be used?

1. Print 1–100 → ?
   ✅ for

2. ATM PIN retry → ?
   ✅ while

3. Multiplication table → ?
   ✅ for

4. Login until correct password → ?
   ✅ while

This helps them remember forever.

