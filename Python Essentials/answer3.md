# Pre-Training Assessment Test – Set 3 (Answer Key)

## Section A: MCQs Answers

| Q.No | Answer                    |
| ---- | ------------------------- |
| 1    | **D) 2.6666666666666665** |
| 2    | **C) print()**            |
| 3    | **D) Set**                |
| 4    | **B) r**                  |
| 5    | **C) continue**           |
| 6    | **B) False**              |
| 7    | **C) first_name**         |
| 8    | **B) remove()**           |
| 9    | **B) 2**                  |
| 10   | **C) if-elif-else**       |
| 11   | **B) `<p>`**              |
| 12   | **C) background-color**   |
| 13   | **A) number**             |
| 14   | **C) len()**              |
| 15   | **A) True**               |
| 16   | **B) ()**                 |
| 17   | **C) 200**                |
| 18   | **A)**                    |

````text id="7szgg9"
*
**
***
``` |
| 19 | **D) import** |
| 20 | **B) .js** |

---

# Section B: Coding Questions – Sample Solutions

## Question 1: Pattern Problem

```python id="g0r6w4"
n = int(input())

for i in range(n, 0, -1):
    print("*" * i)
````

---

## Question 2: Armstrong Number

### Method 1: Generalized Armstrong Number

```python id="3xvlhy"
num = int(input())

temp = num
digits = len(str(num))
sum_value = 0

while temp > 0:
    digit = temp % 10
    sum_value += digit ** digits
    temp //= 10

if sum_value == num:
    print("Armstrong Number")
else:
    print("Not an Armstrong Number")
```

### Method 2: Basic Approach (For 3-digit numbers)

```python id="10hru4"
num = int(input())

temp = num
sum_value = 0

while temp > 0:
    digit = temp % 10
    sum_value += digit ** 3
    temp //= 10

if sum_value == num:
    print("Armstrong Number")
else:
    print("Not an Armstrong Number")
```

---

## Question 3: Count Even and Odd Numbers

```python id="7vqvch"
numbers = [1, 2, 3, 4, 5, 6]

even_count = 0
odd_count = 0

for num in numbers:
    if num % 2 == 0:
        even_count += 1
    else:
        odd_count += 1

print("Even Count:", even_count)
print("Odd Count:", odd_count)
```

---

## Evaluation Criteria

| Score Range | Level                      |
| ----------- | -------------------------- |
| **0–15**    | Beginner                   |
| **16–30**   | Intermediate               |
| **31–40**   | Good                       |
| **41–50**   | Advanced / Placement Ready |
