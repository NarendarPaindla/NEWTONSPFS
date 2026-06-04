# Type Hints in Python

## What are Type Hints?

Type hints are annotations used to indicate the expected data type of variables, function parameters, and return values in Python.

They improve:

* Code readability
* Code maintainability
* IDE support (auto-completion and error detection)
* Static type checking using tools like `mypy`

**Note:** Python does not enforce type hints at runtime. They are mainly used for documentation and development tools.

---

## 1. Type Hints for Variables

```python
name: str = "Narendar"
age: int = 25
salary: float = 50000.50
is_active: bool = True
```

### Explanation

* `str` → String
* `int` → Integer
* `float` → Decimal number
* `bool` → Boolean (True/False)

---

## 2. Type Hints for Function Parameters

```python
def greet(name: str) -> None:
    print(f"Hello {name}")
```

### Example

```python
greet("John")
```

**Output**

```
Hello John
```

---

## 3. Type Hints for Return Values

```python
def add(a: int, b: int) -> int:
    return a + b
```

### Example

```python
result = add(10, 20)
print(result)
```

**Output**

```
30
```

---

## 4. Multiple Parameter Types

```python
def calculate_area(length: float, width: float) -> float:
    return length * width
```

---

## 5. List Type Hint

```python
def get_total(numbers: list[int]) -> int:
    return sum(numbers)
```

### Example

```python
nums = [10, 20, 30]
print(get_total(nums))
```

**Output**

```
60
```

---

## 6. Tuple Type Hint

```python
def get_student() -> tuple[int, str]:
    return (101, "John")
```

---

## 7. Dictionary Type Hint

```python
def get_employee() -> dict[str, int]:
    return {
        "salary": 50000,
        "bonus": 10000
    }
```

---

## 8. Set Type Hint

```python
def get_unique_numbers() -> set[int]:
    return {1, 2, 3, 4}
```

---

## 9. Optional Type Hint

Used when a value can be a specific type or `None`.

```python
from typing import Optional

def get_name(name: Optional[str]) -> str:
    if name:
        return name
    return "Unknown"
```

---

## 10. Union Type Hint

Used when a parameter can accept multiple types.

```python
from typing import Union

def display(value: Union[int, str]) -> None:
    print(value)
```

### Example

```python
display(100)
display("Python")
```

---

## 11. Any Type Hint

Used when a variable can hold any type of value.

```python
from typing import Any

def show(data: Any) -> None:
    print(data)
```

---

## 12. Type Hint in Classes

```python
class Student:
    def __init__(self, sid: int, name: str):
        self.sid: int = sid
        self.name: str = name

    def display(self) -> None:
        print(f"ID: {self.sid}")
        print(f"Name: {self.name}")
```

### Example

```python
s = Student(101, "Narendar")
s.display()
```

---

## 13. Type Hint with Custom Objects

```python
class Employee:
    pass

def process(emp: Employee) -> Employee:
    return emp
```

---

## 14. Type Hint for Callable Functions

```python
from typing import Callable

def execute(operation: Callable[[int, int], int]) -> int:
    return operation(10, 20)

def add(a: int, b: int) -> int:
    return a + b

print(execute(add))
```

**Output**

```
30
```

---

## Benefits of Type Hints

1. Improves code readability.
2. Makes debugging easier.
3. Helps IDEs provide better suggestions.
4. Supports static type checking.
5. Makes large projects easier to maintain.

---

## Complete Example

```python
from typing import Optional

def calculate_salary(
    basic: float,
    bonus: Optional[float] = None
) -> float:

    if bonus:
        return basic + bonus

    return basic


salary = calculate_salary(50000, 5000)
print(salary)
```

**Output**

```
55000.0
```

### Summary

| Type Hint | Example                     |
| --------- | --------------------------- |
| int       | `age: int`                  |
| float     | `salary: float`             |
| str       | `name: str`                 |
| bool      | `status: bool`              |
| list      | `list[int]`                 |
| tuple     | `tuple[int, str]`           |
| dict      | `dict[str, int]`            |
| set       | `set[int]`                  |
| Optional  | `Optional[str]`             |
| Union     | `Union[int, str]`           |
| Any       | `Any`                       |
| Callable  | `Callable[[int, int], int]` |

**Type hints describe what type of data is expected, making Python code clearer, safer, and easier to maintain.**
