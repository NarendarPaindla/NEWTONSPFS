# Pre-Training Assessment Test – Set 2 (Answer Key)

## Section A: MCQs Answers

| Q.No | Answer             |
| ---- | ------------------ |
| 1    | **D) 1**           |
| 2    | **B) input()**     |
| 3    | **D) Dictionary**  |
| 4    | **B) P**           |
| 5    | **C) break**       |
| 6    | **B) True**        |
| 7    | **D) Tuple**       |
| 8    | **C) #**           |
| 9    | **B) 4**           |
| 10   | **C) if**          |
| 11   | **B) `<img>`**     |
| 12   | **C) font-weight** |
| 13   | **C) 7**           |
| 14   | **C) append()**    |
| 15   | **B) True**        |
| 16   | **B) class**       |
| 17   | **C) 404**         |
| 18   | **B)**             |

````text id="jdbjv6"
0
1
2
``` |
| 19 | **C) const** |
| 20 | **D) .css** |

---

# Section B: Coding Questions – Sample Solutions

## Question 1: Pattern Problem

```python id="d0fj67"
n = int(input())

for i in range(1, n + 1):
    for j in range(1, i + 1):
        print(j, end="")
    print()
````

---

## Question 2: Palindrome Number

### Method 1: Using String Slicing

```python id="0ewr3g"
num = input()

if num == num[::-1]:
    print("Palindrome")
else:
    print("Not Palindrome")
```

### Method 2: Without String Slicing

```python id="f70z4u"
num = int(input())
temp = num
reverse = 0

while temp > 0:
    digit = temp % 10
    reverse = reverse * 10 + digit
    temp //= 10

if num == reverse:
    print("Palindrome")
else:
    print("Not Palindrome")
```

---

## Question 3: Find Largest Number Without `max()`

```python id="kq6z0w"
numbers = [4, 8, 2, 15, 10]

largest = numbers[0]

for num in numbers:
    if num > largest:
        largest = num

print(largest)
```

---

## Evaluation Criteria

| Score Range | Level                      |
| ----------- | -------------------------- |
| **0–15**    | Beginner                   |
| **16–30**   | Intermediate               |
| **31–40**   | Good                       |
| **41–50**   | Advanced / Placement Ready |
