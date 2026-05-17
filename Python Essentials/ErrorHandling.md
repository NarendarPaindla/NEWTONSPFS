## Concept 7: Error Handling (Exception Handling) — Placement + Industry Ready

### Goal of this Module

By end of class students should:

✅ Understand types of errors
✅ Prevent program crashes
✅ Use `try-except-finally` properly
✅ Handle real-world failures
✅ Debug exceptions confidently
✅ Answer placement interview questions
✅ Learn custom exceptions

---

# 1. What is Error Handling?

### Definition

Error handling means:

### **Managing program errors gracefully without crashing the application**

Without handling:

Program crashes ❌

With handling:

Program continues safely ✅

---

## Real World Example

### ATM Withdrawal

Without error handling:

User enters:

```text
abc
```

App crashes ❌

With error handling:

```text
Invalid amount entered
Please try again
```

Professional software behavior.

---

# 2. Types of Errors in Python

Python has mainly:

### 1. Syntax Errors

### 2. Runtime Errors (Exceptions)

### 3. Logical Errors

Very important interview question.

---

# 3. Syntax Errors

Errors in writing code.

Example:

```python id="v1ksr2"
if True
    print("Hello")
```

Error:

```python id="a91md0"
SyntaxError
```

Why?

Missing colon (`:`)

---

### Common Syntax Mistakes

```python id="rf8k1m"
if age > 18
```

Missing `:`

---

```python id="ur9p3l"
print("Hello"
```

Bracket missing.

---

### Important Teaching Point

Syntax errors:

### Cannot be handled using try-except.

Interview favorite.

---

# 4. Runtime Errors (Exceptions)

Program runs.

Then crashes.

Example:

```python id="wr1gm9"
print(10 / 0)
```

Error:

```python id="t8l5uy"
ZeroDivisionError
```

---

### More Examples

#### NameError

```python id="r8qtwo"
print(age)
```

Variable missing.

---

#### TypeError

```python id="v7pl2m"
print("10" + 10)
```

Different datatypes.

---

#### IndexError

```python id="9v1qyd"
nums = [1,2]

print(nums[5])
```

Invalid index.

---

#### KeyError

```python id="gr3mxa"
student = {"name": "Narendar"}

print(student["age"])
```

Key missing.

---

#### ValueError

```python id="z6wqfy"
num = int("abc")
```

Wrong conversion.

---

#### FileNotFoundError

```python id="j9tv0o"
open("data.txt")
```

File missing.

---

# 5. Logical Errors

Most dangerous.

Program runs.

Wrong output.

Example:

Wrong:

```python id="x9uy8j"
marks = 90

if marks > 100:
    print("Pass")
```

No crash.

But logic wrong.

Hard to debug.

Interview answer.

---

# 6. What is Exception?

Exception means:

### Unexpected event interrupting program flow.

Example:

```python id="w2mtzq"
10 / 0
```

Python creates:

### Exception Object

Very hidden concept.

---

# 7. try-except Block

Basic syntax.

```python id="7h1p4y"
try:
    risky_code

except:
    handling_code
```

Example:

```python id="42g0ku"
try:
    num = int(input("Enter number: "))
    print(10 / num)

except:
    print("Something went wrong")
```

---

## Hidden Interview Concept

Avoid generic exception.

Bad:

```python id="ut1r4q"
except:
```

Why bad?

Catches everything.

Hard debugging.

Professional approach:

Specific exception.

---

# 8. Specific Exception Handling

Better way.

Example:

```python id="3x9jtm"
try:
    num = int(input())

    print(10 / num)

except ZeroDivisionError:
    print("Cannot divide by zero")

except ValueError:
    print("Enter valid number")
```

Industry best practice.

---

# 9. Multiple Exceptions

Example:

```python id="8vgqde"
try:
    nums = [1,2]

    print(nums[5])

except (IndexError, TypeError):
    print("Handled")
```

Interview question.

---

# 10. Exception Object (Very Important)

Capture actual error.

Example:

```python id="5xrqow"
try:
    print(10 / 0)

except Exception as e:
    print(e)
```

Output:

```text
division by zero
```

### Hidden Concept

`e`

Contains actual exception object.

Professional debugging.

---

# 11. else Block

Most students never know.

Runs when:

### No exception occurs.

Example:

```python id="3op8dt"
try:
    print(10 / 2)

except:
    print("Error")

else:
    print("Success")
```

Output:

```python id="jlwmh1"
5
Success
```

---

# 12. finally Block (Very Important)

Runs:

### Always

Even if error occurs.

Used for:

* Database close
* File close
* API cleanup

Example:

```python id="jlwmh2"
try:
    print(10 / 0)

except:
    print("Error")

finally:
    print("Always runs")
```

Output:

```python id="jlwmh3"
Error
Always runs
```

---

### Hidden Interview Question

Will finally execute after return?

Yes.

Example:

```python id="jlwmh4"
def test():

    try:
        return 1

    finally:
        print("Cleaning")

print(test())
```

Output:

```python id="jlwmh5"
Cleaning
1
```

Very advanced interview question.

---

# 13. Raising Exceptions

Create your own error.

Example:

```python id="jlwmh6"
age = 15

if age < 18:
    raise Exception("Not eligible")
```

Output:

```text
Exception: Not eligible
```

---

### Better Practice

Use specific exception.

```python id="jlwmh7"
raise ValueError("Invalid age")
```

Industry standard.

---

# 14. Custom Exceptions (Advanced)

Very important for real projects.

Example:

```python id="jlwmh8"
class InsufficientBalance(Exception):
    pass


balance = 1000
withdraw = 2000

if withdraw > balance:
    raise InsufficientBalance(
        "Low Balance"
    )
```

Industry use:

* Banking
* Authentication
* Payments

---

# 15. Hidden Python Concepts (Rare)

---

## Exceptions are Objects

Example:

```python id="jlwmh9"
print(type(ValueError()))
```

Output:

```python id="jlwmha0"
<class 'ValueError'>
```

Most students don’t know.

---

## Exception Hierarchy

All exceptions inherit from:

### BaseException

Important interview topic.

Common hierarchy:

```text
Exception
├── ValueError
├── TypeError
├── IndexError
├── KeyError
├── ZeroDivisionError
```

---

## `except Exception`

Catches most errors.

But:

Avoid:

```python id="jlwmha1"
except BaseException
```

Why?

Catches system exit/interruption too.

Bad practice.

---

# 16. Real World Example

### Banking Withdrawal System

```python id="jlwmha2"
balance = 1000

try:
    amount = int(input())

    if amount > balance:
        raise ValueError(
            "Insufficient Balance"
        )

    balance -= amount

    print("Withdraw Success")

except ValueError as e:
    print(e)

finally:
    print("Transaction Ended")
```

Excellent real-world example.

---

# 17. Tricky Placement Questions

### Question 1

Output?

```python id="jlwmha3"
try:
    print(10 / 0)

except:
    print("Error")

finally:
    print("Done")
```

Answer:

```python id="jlwmha4"
Error
Done
```

---

### Question 2

Output?

```python id="jlwmha5"
try:
    print("Hello")

except:
    print("Error")

else:
    print("Success")
```

Answer:

```python id="jlwmha6"
Hello
Success
```

---

### Question 3

Output?

```python id="jlwmha7"
try:
    return 1

finally:
    print("Cleanup")
```

Answer:

Finally executes first.

Huge interview question.

---

### Question 4

```python id="jlwmha8"
try:
    print("10" + 10)

except TypeError:
    print("Handled")
```

Output:

```python id="jlwmha9"
Handled
```

---

### Question 5

```python id="jlwmhb0"
try:
    nums = [1]

    print(nums[5])

except Exception as e:
    print(type(e))
```

Answer:

```python id="jlwmhb1"
<class 'IndexError'>
```

---

# 18. Placement Interview Questions + Answers

### Q1: Difference between syntax error and exception?

### Answer

Syntax Error:

Code structure wrong.

Program won't run.

Exception:

Program runs but crashes during execution.

---

### Q2: Difference between error and exception?

### Answer

Exception can be handled.

Error usually severe or logical/system-related.

---

### Q3: Why avoid generic except?

### Answer

Hard debugging.

Hides real problem.

Bad practice.

---

### Q4: Difference between finally and else?

### Answer

`else`

Runs if no exception.

`finally`

Runs always.

---

### Q5: Why custom exceptions?

### Answer

Better business logic handling.

Readable code.

Professional systems.

---

### Q6: Why exception handling important?

### Answer

Prevents crashes.

Improves user experience.

Safer applications.

---

# 19. Live Practical Activity

### Student Login System

Handle:

* Wrong password
* Invalid input
* Retry system

Using:

* try
* except
* finally

---

# 20. Class Assignments

## Level 1 (Basic)

1. Divide numbers safely
2. Invalid age input
3. Handle index errors

---

## Level 2 (Intermediate)

1. Calculator with exception handling
2. ATM withdrawal system
3. Login validation

---

## Level 3 (Placement)

1. Predict outputs
2. Multiple exception handling
3. Build custom exception

---

## Level 4 (Real World)

Build:

### Banking Management System

Features:

* Withdraw
* Deposit
* Validation
* Custom exception
* Transaction cleanup

---

# Secret Concepts Students Usually Don’t Know

### 1. finally executes even after return

Very advanced.

---

### 2. Exceptions are objects

Rarely taught.

---

### 3. Avoid bare except

Industry best practice.

---

### 4. Exception hierarchy

Interview favorite.

---

### 5. else block exists in try-except

Most students never know.

---

### End-of-Class Assessment

Ask:

1. Difference between syntax & runtime error?
2. Why generic except bad?
3. Difference between else & finally?
4. Why custom exception?
5. Explain exception hierarchy?
6. Predict tricky outputs.

Next concept:

## **File I/O (File Handling)** — placement + industry + hidden file system concepts.
