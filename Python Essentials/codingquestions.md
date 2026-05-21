# PYTHON CODING PRACTICE SHEET

## Topic: Variables, Data Types & Operators Only


---

# LEVEL 1 — BASIC CONFIDENCE BUILDING

## Problem 1: Print Personal Information

### Problem Statement

Write a Python program to store your name, age, and percentage in variables and print them.

### Input Format

No input.

### Output Format

Print the details.

### Sample Output

```text
Name: Rahul
Age: 22
Percentage: 85.5
```

### Answer

```python
name = "Rahul"
age = 22
percentage = 85.5

print("Name:", name)
print("Age:", age)
print("Percentage:", percentage)
```

---

## Problem 2: Add Two Numbers

### Problem Statement

Write a Python program to take two integers and print their sum.

### Input Format

Two integers.

### Output Format

Print the sum.

### Sample Input

```text
10
20
```

### Sample Output

```text
30
```

### Answer

```python
a = int(input())
b = int(input())

sum_result = a + b

print(sum_result)
```

---

## Problem 3: Find Product of Two Numbers

### Problem Statement

Take two numbers as input and print their multiplication.

### Input Format

Two integers.

### Output Format

Print multiplication result.

### Sample Input

```text
5
6
```

### Sample Output

```text
30
```

### Answer

```python
a = int(input())
b = int(input())

product = a * b

print(product)
```

---

## Problem 4: Area of Rectangle

### Problem Statement

Find the area of a rectangle.

### Formula

Area = length × breadth

### Input Format

Length and breadth.

### Output Format

Area of rectangle.

### Sample Input

```text
5
4
```

### Sample Output

```text
20
```

### Answer

```python
length = float(input())
breadth = float(input())

area = length * breadth

print(area)
```

---

## Problem 5: Area of Circle

### Problem Statement

Find the area of a circle.

Formula:
Area = π × r²

Take π = 3.14

### Input Format

Radius

### Output Format

Area

### Sample Input

```text
7
```

### Sample Output

```text
153.86
```

### Answer

```python
radius = float(input())

area = 3.14 * radius * radius

print(area)
```

---

## Problem 6: Celsius to Fahrenheit

### Problem Statement

Convert Celsius to Fahrenheit.

Formula:
F = (C × 9/5) + 32

### Input Format

Temperature in Celsius

### Output Format

Temperature in Fahrenheit

### Sample Input

```text
25
```

### Sample Output

```text
77.0
```

### Answer

```python
celsius = float(input())

fahrenheit = (celsius * 9/5) + 32

print(fahrenheit)
```

---

## Problem 7: Swap Two Numbers Using Third Variable

### Problem Statement

Swap two numbers.

### Input Format

Two integers

### Output Format

Swapped numbers

### Sample Input

```text
10
20
```

### Sample Output

```text
20
10
```

### Answer

```python
a = int(input())
b = int(input())

temp = a
a = b
b = temp

print(a)
print(b)
```

---

## Problem 8: Swap Without Third Variable

### Problem Statement

Swap two numbers without using a third variable.

### Input Format

Two integers

### Output Format

Swapped values

### Sample Input

```text
5
9
```

### Sample Output

```text
9
5
```

### Answer

```python
a = int(input())
b = int(input())

a = a + b
b = a - b
a = a - b

print(a)
print(b)
```

---

## Problem 9: Simple Interest

### Problem Statement

Calculate Simple Interest.

Formula:
SI = (P × T × R)/100

### Input Format

Principal, Time, Rate

### Output Format

Simple Interest

### Sample Input

```text
10000
2
5
```

### Sample Output

```text
1000.0
```

### Answer

```python
p = float(input())
t = float(input())
r = float(input())

si = (p * t * r) / 100

print(si)
```

---

## Problem 10: Compound Interest

### Problem Statement

Calculate compound interest.

Formula:
A = P(1 + R/100)^T

CI = A − P

### Input Format

Principal, Rate, Time

### Output Format

Compound Interest

### Sample Input

```text
1000
10
2
```

### Sample Output

```text
210.0
```

### Answer

```python
p = float(input())
r = float(input())
t = float(input())

amount = p * (1 + r/100) ** t
ci = amount - p

print(ci)
```

---

# LEVEL 2 — DATATYPES + TYPE CASTING

## Problem 11: Convert Float to Integer

### Problem Statement

Take a float number and convert it into integer.

### Input Format

Float value

### Output Format

Integer value

### Sample Input

```text
10.99
```

### Sample Output

```text
10
```

### Answer

```python
num = float(input())

result = int(num)

print(result)
```

---

## Problem 12: ASCII Value of Character

### Problem Statement

Find ASCII value of a character.

### Input Format

Character

### Output Format

ASCII value

### Sample Input

```text
A
```

### Sample Output

```text
65
```

### Answer

```python
ch = input()

print(ord(ch))
```

---

## Problem 13: Character from ASCII

### Problem Statement

Convert ASCII value into character.

### Input Format

ASCII number

### Output Format

Character

### Sample Input

```text
97
```

### Sample Output

```text
a
```

### Answer

```python
num = int(input())

print(chr(num))
```

---

## Problem 14: Average of 5 Numbers

### Problem Statement

Find average of 5 numbers.

### Input Format

Five integers

### Output Format

Average

### Sample Input

```text
10
20
30
40
50
```

### Sample Output

```text
30.0
```

### Answer

```python
a = int(input())
b = int(input())
c = int(input())
d = int(input())
e = int(input())

average = (a + b + c + d + e) / 5

print(average)
```

---

## Problem 15: Square and Cube

### Problem Statement

Find square and cube of a number.

### Input Format

Integer

### Output Format

Square and cube

### Sample Input

```text
4
```

### Sample Output

```text
16
64
```

### Answer

```python
n = int(input())

square = n ** 2
cube = n ** 3

print(square)
print(cube)
```

---

# LEVEL 3 — TRICKY OPERATOR PROBLEMS

## Problem 16: Quotient and Remainder

### Problem Statement

Find quotient and remainder.

### Input Format

Dividend and divisor

### Output Format

Quotient and remainder

### Sample Input

```text
17
5
```

### Sample Output

```text
3
2
```

### Answer

```python
a = int(input())
b = int(input())

quotient = a // b
remainder = a % b

print(quotient)
print(remainder)
```

---

## Problem 17: Reverse a 3 Digit Number

### Problem Statement

Reverse a 3-digit number using operators.

### Input Format

3-digit integer

### Output Format

Reversed number

### Sample Input

```text
456
```

### Sample Output

```text
654
```

### Answer

```python
n = int(input())

reverse = (n % 10) * 100 + ((n // 10) % 10) * 10 + (n // 100)

print(reverse)
```

---

## Problem 18: Sum of Digits (3-digit Number)

### Problem Statement

Find sum of digits.

### Input Format

3-digit number

### Output Format

Digit sum

### Sample Input

```text
123
```

### Sample Output

```text
6
```

### Answer

```python
n = int(input())

sum_digits = (n % 10) + ((n // 10) % 10) + (n // 100)

print(sum_digits)
```

---

## Problem 19: Find Last Digit

### Problem Statement

Find last digit of a number.

### Input Format

Integer

### Output Format

Last digit

### Sample Input

```text
4578
```

### Sample Output

```text
8
```

### Answer

```python
n = int(input())

print(n % 10)
```

---

## Problem 20: Remove Last Digit

### Problem Statement

Remove last digit.

### Input Format

Integer

### Output Format

Number without last digit

### Sample Input

```text
4567
```

### Sample Output

```text
456
```

### Answer

```python
n = int(input())

print(n // 10)
```

This is **Part 1 (20 problems)** from **basic → tricky**.

Next I’ll continue with **Level 4 & 5 (placement tricky problems)**:

* operator precedence traps
* bitwise operator tricks
* salary/tax calculation
* digital root without loops
* hidden datatype conversions
* expression-based interview puzzles
* mathematical logic problems
* advanced placement questions (no conditions/loops)
