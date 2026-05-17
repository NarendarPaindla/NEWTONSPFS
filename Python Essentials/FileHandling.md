## Concept 8: File I/O (File Handling) — Placement + Industry Ready

### Goal of this Module

By end of class students should:

✅ Read & write files confidently
✅ Understand file modes deeply
✅ Handle real-world files safely
✅ Avoid common file handling bugs
✅ Learn CSV/JSON basics for industry
✅ Answer placement interview questions
✅ Understand memory-efficient file reading

---

# 1. What is File I/O?

### Definition

File I/O means:

### **Reading data from files and writing data into files**

I/O means:

### Input / Output

Input → Reading data
Output → Writing data

---

## Real World Examples

Where file handling is used?

* Student Management System
* Logs
* Reports
* Resume parser
* CSV datasets
* Authentication logs
* ML datasets

Industry-heavy topic.

---

# 2. Why File Handling Needed?

Without files:

Data lost after program ends.

Example:

```python id="k6rtf1"
name = "Narendar"
```

Program closes.

Data gone ❌

Using files:

Data persists ✅

---

# 3. What is a File?

A file is:

### Permanent storage of information.

Types:

* `.txt`
* `.csv`
* `.json`
* `.pdf`
* `.xlsx`

For placements:

Focus:

### Text Files

---

# 4. Opening a File

Syntax:

```python id="j8q2kw"
file = open(
    "filename",
    "mode"
)
```

Example:

```python id="q2nw91"
file = open("data.txt", "r")
```

---

## Hidden Concept

Opening file:

### OS allocates file descriptor

Very advanced concept.

Interview point.

---

# 5. File Modes (Most Important)

| Mode | Meaning      |
| ---- | ------------ |
| `r`  | Read         |
| `w`  | Write        |
| `a`  | Append       |
| `x`  | Create       |
| `r+` | Read + Write |
| `b`  | Binary       |

Must memorize.

Interview favorite.

---

# 6. Read Mode (`r`)

Reads file.

Example:

File:

```text id="ezjlwm"
Hello Python
Welcome Students
```

Code:

```python id="jlwmhc1"
file = open("data.txt", "r")

content = file.read()

print(content)

file.close()
```

Output:

```text id="jlwmhc2"
Hello Python
Welcome Students
```

---

### Hidden Trap

If file missing:

```python id="jlwmhc3"
open("abc.txt", "r")
```

Error:

```text id="jlwmhc4"
FileNotFoundError
```

Interview question.

---

# 7. Write Mode (`w`)

Creates file.

If exists:

### Overwrites everything

Very dangerous.

Example:

```python id="jlwmhc5"
file = open("data.txt", "w")

file.write("Hello")

file.close()
```

---

### Hidden Interview Question

What happens if file exists?

### Answer

Old content deleted.

Overwritten.

Very important.

---

# 8. Append Mode (`a`)

Adds content.

Does not delete existing data.

Example:

```python id="jlwmhc6"
file = open("data.txt", "a")

file.write("\nNew Student")

file.close()
```

Industry logging uses this.

---

# 9. Exclusive Create (`x`)

Creates new file only.

If exists:

Error.

Example:

```python id="jlwmhc7"
file = open("new.txt", "x")
```

---

# 10. Closing Files

Important.

Example:

```python id="jlwmhc8"
file.close()
```

Why?

Releases system resources.

Avoids memory/resource leaks.

Interview answer.

---

## Hidden Danger

Without close:

* File lock issues
* Memory waste
* Corrupted writes

Industry issue.

---

# 11. Best Practice: `with open()`

Most important.

Professional way.

Example:

```python id="jlwmhc9"
with open(
    "data.txt",
    "r"
) as file:

    content = file.read()

    print(content)
```

No need:

```python id="jlwmhd0"
file.close()
```

Automatically closes.

Industry standard.

---

### Hidden Interview Question

Why `with open()` preferred?

### Answer

Automatic cleanup.

Safer.

Prevents resource leak.

Cleaner code.

---

# 12. Reading File Methods

---

## `read()`

Reads entire file.

Example:

```python id="jlwmhd1"
file.read()
```

---

### Hidden Danger

Bad for huge files.

Loads entire memory.

Big company interview topic.

---

## `readline()`

Reads one line.

Example:

```python id="jlwmhd2"
print(file.readline())
```

---

## `readlines()`

Returns list.

Example:

```python id="jlwmhd3"
lines = file.readlines()

print(lines)
```

Output:

```python id="jlwmhd4"
[
 'Hello\n',
 'Python\n'
]
```

---

### Best Industry Method

Loop line by line.

Memory efficient.

```python id="’winihd5"
with open("data.txt") as file:

    for line in file:
        print(line.strip())
```

Huge interview point.

---

# 13. Writing to File

Example:

```python id="jlwmhd6"
with open(
    "student.txt",
    "w"
) as file:

    file.write(
        "Narendar\n"
    )

    file.write("24")
```

---

### Multiple Lines

```python id="’winihd7"
data = [
    "Java\n",
    "Python\n",
    "React\n"
]

file.writelines(data)
```

---

# 14. File Pointer (Hidden Concept)

Very important.

Example:

```python id="’winihd8"
file = open("data.txt")

print(file.read())

print(file.read())
```

Second output?

Empty.

Why?

Pointer at end.

---

### Move Pointer

Using:

```python id="’winihd9"
file.seek(0)
```

Reset position.

Interview favorite.

---

### Current Position

```python id="’winihe0"
print(file.tell())
```

Returns cursor position.

Advanced interview.

---

# 15. Exception Handling + File

Professional approach.

Example:

```python id="’winihe1"
try:

    with open(
        "data.txt"
    ) as file:

        print(
            file.read()
        )

except FileNotFoundError:

    print(
        "File missing"
    )
```

Industry standard.

---

# 16. Working with CSV (Industry)

Very important.

Example:

```python id="’winihe2"
import csv

with open(
    "students.csv"
) as file:

    reader = csv.reader(file)

    for row in reader:
        print(row)
```

Used in:

* Data analytics
* ML
* Reports

---

# 17. Working with JSON (Industry)

Very important.

Example:

```python id="’winihe3"
import json

student = {
    "name": "Narendar",
    "age": 24
}

with open(
    "data.json",
    "w"
) as file:

    json.dump(
        student,
        file
    )
```

Read:

```python id="’winihe4"
with open(
    "data.json"
) as file:

    data = json.load(file)

print(data)
```

Industry must-know.

---

# 18. Hidden Concepts Students Rarely Know

---

## File Buffering

Python buffers file operations.

Improves performance.

Very advanced interview point.

---

## Encoding Issue

Example:

```python id="’winihe5"
with open(
    "data.txt",
    encoding="utf-8"
)
```

Why?

Special characters support.

Real-world issue.

---

## Text vs Binary Mode

Text:

```python id="’winihe6"
"r"
```

Binary:

```python id="’winihe7"
"rb"
```

For:

* Images
* PDFs
* Videos

---

## Relative vs Absolute Path

Relative:

```python id="’winihe8"
data.txt
```

Absolute:

```python id="’winihe9"
C:/Users/file.txt
```

Interview point.

---

# 19. Tricky Placement Questions

### Question 1

Output?

```python id="’winihf0"
with open(
    "data.txt",
    "w"
) as file:

    file.write("Hello")
```

If file exists?

### Answer

Old data removed.

---

### Question 2

Output?

```python id="’winihf1"
file = open("data.txt")

print(file.read())

print(file.read())
```

Answer:

Second read:

Empty string.

Pointer at end.

---

### Question 3

Best method for huge file?

### Answer

Line by line:

```python id="’winihf2"
for line in file
```

Memory efficient.

---

### Question 4

Why `with open()`?

### Answer

Automatic file close.

Safer.

Cleaner.

---

### Question 5

Difference:

`read()`

vs

`readline()`

### Answer

`read()`

Entire file.

`readline()`

Single line.

---

# 20. Placement Interview Questions + Answers

### Q1: Difference between `w` and `a`?

### Answer

`w`

Overwrite file.

`a`

Append data.

---

### Q2: Why close file important?

### Answer

Releases resources.

Prevents corruption.

Avoids memory leak.

---

### Q3: Why `with open()` preferred?

### Answer

Automatic cleanup.

Cleaner syntax.

Industry best practice.

---

### Q4: Difference between text and binary mode?

### Answer

Text → strings

Binary → bytes

Used for media files.

---

### Q5: Best way to read huge file?

### Answer

Line-by-line iteration.

Memory efficient.

---

### Q6: What is seek()?

### Answer

Moves file pointer.

---

# 21. Live Practical Activity

### Student File Management System

Menu:

```text
1 Add Student
2 View Students
3 Search Student
4 Exit
```

Store in:

```text
students.txt
```

Students practice:

* write
* read
* append
* exception handling

Excellent mini project.

---

# 22. Class Assignments

## Level 1 (Basic)

1. Read file
2. Write file
3. Append file

---

## Level 2 (Intermediate)

1. Notes saver
2. Student record system
3. Word counter

---

## Level 3 (Placement)

1. File copy utility
2. Search word in file
3. Count lines/words/chars

---

## Level 4 (Real World)

Build:

### Employee Record Manager

Features:

* Add employee
* Search employee
* Update record
* Save permanently

---

# Secret Concepts Students Usually Don’t Know

### 1. File pointer behavior

Very important.

---

### 2. `with open()` auto close

Industry must.

---

### 3. Large file memory issue

Interview favorite.

---

### 4. Encoding problem

Real-world bug.

---

### 5. File buffering

Advanced interview topic.

---

## End-of-Class Assessment

Ask:

1. Difference between `w` & `a`?
2. Why `with open()`?
3. Why second `read()` empty?
4. What is seek()?
5. Best way to read large files?
6. Difference text vs binary?

### Python Essentials Module Completed ✅

Now students are ready for:

### **Functional Programming in Python** (Functions deep-dive, lambda, map, filter, reduce, closures, decorators basics) 
