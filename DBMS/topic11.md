# 🔥 TOPIC 10: AGGREGATE FUNCTIONS

## “SQL as a Data Calculator”

Now students know `GROUP BY`, so this is the perfect time to deeply understand the functions that **calculate answers from multiple rows**.

Don't begin with:

> “There are five aggregate functions.”

Begin with:

> **“Imagine I give you 10,000 student marks. I don't want to see 10,000 rows. I want answers.”**

For example:

* How many students?
* What is the average?
* What is the highest mark?
* What is the lowest mark?
* What is the total?

That's the job of **aggregate functions**.

---

# 🧠 1. What Is an Aggregate Function?

Tell students:

> **“An aggregate function takes many rows and produces a calculated result.”**

Example:

```text
Marks
-----
80
90
70
60
100
```

Instead of returning all five rows:

```text
80
90
70
60
100
```

SQL can calculate:

```text
COUNT → 5
SUM   → 400
AVG   → 80
MAX   → 100
MIN   → 60
```

---

# ⭐ The Five Functions

| Function  | Meaning   |
| --------- | --------- |
| `COUNT()` | How many? |
| `SUM()`   | Total?    |
| `AVG()`   | Average?  |
| `MAX()`   | Highest?  |
| `MIN()`   | Lowest?   |

Give students the memory trick:

> **COUNT = How many?**
> **SUM = How much total?**
> **AVG = What's the average?**
> **MAX = What's the biggest?**
> **MIN = What's the smallest?**

---

# 🎬 2. Create a Marks Table

Use:

### `students`

| id | name  | department | marks |
| -: | ----- | ---------- | ----: |
|  1 | Rahul | CSE        |    85 |
|  2 | Priya | ECE        |    72 |
|  3 | Arjun | CSE        |    95 |
|  4 | Sneha | EEE        |    68 |
|  5 | Kiran | CSE        |    78 |

---

# 🔢 3. COUNT()

Ask:

> **“How many students are there?”**

### Answer

```sql
SELECT COUNT(*) AS total_students
FROM students;
```

### Output

| total_students |
| -------------: |
|              5 |

---

# 🧠 Why `COUNT(*)`?

Tell students:

> **“`*` here means count the rows.”**

So:

```sql
COUNT(*)
```

means:

> Count every row.

---

# 🎯 CASE 1 — Count CSE Students

Question:

> How many CSE students are there?

### Answer

```sql
SELECT COUNT(*) AS total_students
FROM students
WHERE department = 'CSE';
```

### Output

| total_students |
| -------------: |
|              3 |

Notice the order conceptually:

```text
Students
 ↓
WHERE CSE
 ↓
COUNT
 ↓
3
```

---

# 🔥 4. SUM()

Ask:

> **“What is the total of all marks?”**

### Answer

```sql
SELECT SUM(marks) AS total_marks
FROM students;
```

Calculate:

```text
85 + 72 + 95 + 68 + 78
= 398
```

### Output

| total_marks |
| ----------: |
|         398 |

---

# 🧩 CASE 2 — Total CSE Marks

Question:

> What is the total marks obtained by CSE students?

### Answer

```sql
SELECT SUM(marks) AS total_marks
FROM students
WHERE department = 'CSE';
```

Calculation:

```text
85 + 95 + 78 = 258
```

### Output

| total_marks |
| ----------: |
|         258 |

---

# 🧠 5. AVG()

Ask:

> **“What is the average mark of all students?”**

### Answer

```sql
SELECT AVG(marks) AS average_marks
FROM students;
```

Calculation:

```text
398 / 5
= 79.6
```

### Output

| average_marks |
| ------------: |
|          79.6 |

---

# 🎯 CASE 3 — Average CSE Marks

```sql
SELECT AVG(marks) AS average_marks
FROM students
WHERE department = 'CSE';
```

Calculation:

```text
(85 + 95 + 78) / 3
= 86
```

### Output

| average_marks |
| ------------: |
|            86 |

---

# 🔥 6. MAX()

Question:

> **“Who scored the highest mark?”**

First find the highest mark:

```sql
SELECT MAX(marks) AS highest_marks
FROM students;
```

### Output

```text
95
```

Then:

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MAX(marks)
    FROM students
);
```

### Output

| name  | marks |
| ----- | ----: |
| Arjun |    95 |

🔥 This connects today's topic with your **subqueries** topic.

---

# 🧠 CASE 4 — Lowest Mark

Question:

> Find the lowest mark.

### Answer

```sql
SELECT MIN(marks) AS lowest_marks
FROM students;
```

### Output

```text
68
```

Who got it?

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MIN(marks)
    FROM students
);
```

### Output

| name  | marks |
| ----- | ----: |
| Sneha |    68 |

---

# 🚨 7. The Most Important `COUNT()` Difference

This is worth spending time on because students frequently get confused.

Suppose:

### `students`

| id | name  | marks |
| -: | ----- | ----: |
|  1 | Rahul |    85 |
|  2 | Priya |  NULL |
|  3 | Arjun |    95 |
|  4 | Sneha |  NULL |

Ask:

> **“What is `COUNT(*)`?”**

### Answer:

**4**

Because there are 4 rows.

---

Now:

```sql
SELECT COUNT(marks)
FROM students;
```

### Answer:

**2**

Because `COUNT(column)` ignores `NULL`.

So:

```text
COUNT(*)       → counts rows
COUNT(marks)   → counts non-NULL marks
```

This is extremely important.

---

# 🔥 CASE 5 — Predict Before Running

Data:

| ID | Name | Course |
| -: | ---- | ------ |
|  1 | A    | Python |
|  2 | B    | NULL   |
|  3 | C    | Python |
|  4 | D    | Java   |

Question:

```sql
SELECT COUNT(*)
FROM students;
```

### Answer

**4**

Now:

```sql
SELECT COUNT(course)
FROM students;
```

### Answer

**3**

Because:

```text
Python → counted
NULL   → ignored
Python → counted
Java   → counted
```

---

# 🧠 8. COUNT(DISTINCT)

Now give:

### Enrollments

| Student | Course |
| ------- | ------ |
| Rahul   | Python |
| Rahul   | DBMS   |
| Priya   | Python |
| Arjun   | Python |
| Arjun   | DBMS   |

Question:

> **“How many different courses are being taken?”**

There are:

```text
Python
DBMS
```

Answer:

**2**

SQL:

```sql
SELECT COUNT(DISTINCT course)
FROM enrollments;
```

### Output

| count |
| ----: |
|     2 |

---

# ⭐ Three COUNTs

Make students memorize this distinction:

```text
COUNT(*)
↓
Number of rows

COUNT(column)
↓
Number of non-NULL values

COUNT(DISTINCT column)
↓
Number of unique non-NULL values
```

---

# 🔥 9. Aggregate + GROUP BY

Now we're going to combine today's topic with the previous topic.

Question:

> **“Find the average marks in each department.”**

### Answer

```sql
SELECT
    department,
    AVG(marks) AS average_marks
FROM students
GROUP BY department;
```

### Output

| department | average_marks |
| ---------- | ------------: |
| CSE        |            86 |
| ECE        |            72 |
| EEE        |            68 |

---

# 🧠 What's happening?

SQL creates groups:

```text
CSE
---------
85
95
78

ECE
---------
72

EEE
---------
68
```

Then:

```text
AVG(CSE) = 86
AVG(ECE) = 72
AVG(EEE) = 68
```

---

# 🎯 CASE 6 — Highest Mark Per Department

Question:

> Find the highest mark in each department.

### Answer

```sql
SELECT
    department,
    MAX(marks) AS highest_marks
FROM students
GROUP BY department;
```

### Output

| department | highest_marks |
| ---------- | ------------: |
| CSE        |            95 |
| ECE        |            72 |
| EEE        |            68 |

---

# 🔥 CASE 7 — Lowest Mark Per Department

```sql
SELECT
    department,
    MIN(marks) AS lowest_marks
FROM students
GROUP BY department;
```

### Output

| department | lowest_marks |
| ---------- | -----------: |
| CSE        |           78 |
| ECE        |           72 |
| EEE        |           68 |

---

# 🎯 CASE 8 — Total Marks Per Department

```sql
SELECT
    department,
    SUM(marks) AS total_marks
FROM students
GROUP BY department;
```

### Output

| department | total_marks |
| ---------- | ----------: |
| CSE        |         258 |
| ECE        |          72 |
| EEE        |          68 |

---

# 🧠 CASE 9 — All Five Together

Question:

> Give me a department-wise report containing:
>
> * number of students
> * total marks
> * average marks
> * highest marks
> * lowest marks

### Answer

```sql
SELECT
    department,
    COUNT(*) AS total_students,
    SUM(marks) AS total_marks,
    AVG(marks) AS average_marks,
    MAX(marks) AS highest_marks,
    MIN(marks) AS lowest_marks
FROM students
GROUP BY department;
```

### Output

| Department | Students | Total | Average | Highest | Lowest |
| ---------- | -------: | ----: | ------: | ------: | -----: |
| CSE        |        3 |   258 |      86 |      95 |     78 |
| ECE        |        1 |    72 |      72 |      72 |     72 |
| EEE        |        1 |    68 |      68 |      68 |     68 |

🔥 This is a great **"wow" query** for first-year students.

---

# 💥 CASE 10 — Department With Average Above 80

Now combine:

```text
GROUP BY
+
AVG
+
HAVING
```

Question:

> Find departments whose average marks are greater than 80.

### Answer

```sql
SELECT
    department,
    AVG(marks) AS average_marks
FROM students
GROUP BY department
HAVING AVG(marks) > 80;
```

### Output

| department | average_marks |
| ---------- | ------------: |
| CSE        |            86 |

Remember:

```text
WHERE
→ filters individual rows

HAVING
→ filters groups
```

---

# 🏆 CASE 11 — The “Which Department Wins?” Challenge

Question:

> Which department has the highest average marks?

First:

```sql
SELECT
    department,
    AVG(marks) AS average_marks
FROM students
GROUP BY department
ORDER BY average_marks DESC;
```

### Output

| department | average_marks |
| ---------- | ------------: |
| CSE        |            86 |
| ECE        |            72 |
| EEE        |            68 |

Then the first row is the answer:

**CSE**

---

# 🧠 CASE 12 — Real-World Company Example

Now move away from students.

### `employees`

| Name  | Department | Salary |
| ----- | ---------- | -----: |
| Rahul | IT         |  60000 |
| Priya | IT         |  80000 |
| Arjun | HR         |  50000 |
| Sneha | HR         |  70000 |
| Kiran | IT         |  90000 |

Question:

> What is the average salary in each department?

### Answer

```sql
SELECT
    department,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department;
```

### Output

| Department | Average Salary |
| ---------- | -------------: |
| IT         |       76666.67 |
| HR         |          60000 |

---

# 🔥 CASE 13 — Salary Investigation

Question:

> Which department has more than 2 employees?

### Answer

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) > 2;
```

### Output

| Department | Employee Count |
| ---------- | -------------: |
| IT         |              3 |

---

# 🧩 CASE 14 — Combine Everything

Question:

> Find departments with at least 2 employees, calculate their average salary, and show highest average salary first.

### Answer

```sql
SELECT
    department,
    COUNT(*) AS employee_count,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2
ORDER BY average_salary DESC;
```

### Output

| Department | Employees | Average Salary |
| ---------- | --------: | -------------: |
| IT         |         3 |       76666.67 |
| HR         |         2 |          60000 |

---

# 🚨 VERY IMPORTANT CONCEPT

Students may ask:

> **“Why can't I use WHERE with AVG?”**

Wrong:

```sql
WHERE AVG(salary) > 70000
```

Correct:

```sql
HAVING AVG(salary) > 70000
```

Because `AVG()` is calculated **after grouping**.

---

# 🏆 FINAL CHALLENGE

Give students:

### `employees`

| ID | Name | Dept  | Salary |
| -: | ---- | ----- | -----: |
|  1 | A    | IT    |  50000 |
|  2 | B    | IT    |  70000 |
|  3 | C    | IT    |  90000 |
|  4 | D    | HR    |  60000 |
|  5 | E    | HR    |  80000 |
|  6 | F    | Sales |  40000 |

Question:

> **Find departments having at least 2 employees, show employee count, total salary, average salary, highest salary and lowest salary, and sort by average salary from highest to lowest.**

### ✅ Answer

```sql
SELECT
    department,
    COUNT(*) AS employee_count,
    SUM(salary) AS total_salary,
    AVG(salary) AS average_salary,
    MAX(salary) AS highest_salary,
    MIN(salary) AS lowest_salary
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2
ORDER BY average_salary DESC;
```

### Output

| Department | Employees |  Total | Average | Highest | Lowest |
| ---------- | --------: | -----: | ------: | ------: | -----: |
| IT         |         3 | 210000 |   70000 |   90000 |  50000 |
| HR         |         2 | 140000 |   70000 |   80000 |  60000 |

Sales disappears because:

```text
Sales → only 1 employee
```

and:

```sql
HAVING COUNT(*) >= 2
```

removes it.

---

# 🧠 FINAL MEMORY MAP

Tell your students:

```text
COUNT() → How many?
SUM()   → Total?
AVG()   → Average?
MAX()   → Highest?
MIN()   → Lowest?
```

Then:

```text
GROUP BY
    ↓
Make groups
    ↓
Aggregate functions
    ↓
Calculate something for each group
    ↓
HAVING
    ↓
Filter the groups
```

### One powerful sentence:

> **“GROUP BY creates the boxes; aggregate functions calculate inside the boxes; HAVING decides which boxes survive.”**

That sentence will make the next topics much easier.

---

## 🔜 NEXT TOPIC: SUBQUERIES — FROM SIMPLE TO ADVANCED

We'll now return to **Subqueries** and do them properly:

**single-row subquery → multi-row subquery → `IN` → `NOT IN` → `ANY` → `ALL` → correlated subquery**, with detective-style problems such as:

> “Find students scoring above the average.”

> “Find students who scored more than every ECE student.”

> “Find the second-highest salary.”

> “Find employees earning more than their department average.”

And every case will include the **answer + query + expected output + line-by-line explanation**.
