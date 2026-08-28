# 🔥 TOPIC 9: JOIN + GROUP BY

## “Turn Rows Into Answers”

Now we're moving from:

> **“Show me the data.”**

to:

> **“Tell me something about the data.”**

This is the point where students start feeling that SQL is actually **powerful**.

---

# 🎬 1. Start With a Real Question

Tell your students:

> **“The Dean doesn't want a list of 1,000 students. He wants to know how many students are in each department.”**

That's a completely different question.

We have:

### `students`

| student_id | name  | dept_id |
| ---------: | ----- | ------: |
|        101 | Rahul |      10 |
|        102 | Priya |      20 |
|        103 | Arjun |      10 |
|        104 | Sneha |      30 |
|        105 | Kiran |      10 |

### `departments`

| dept_id | dept_name |
| ------: | --------- |
|      10 | CSE       |
|      20 | ECE       |
|      30 | EEE       |

---

# 🧠 2. First Get the Data

We already know how to JOIN.

```sql id="5b9p0k"
SELECT d.dept_name, s.name
FROM students s
JOIN departments d
ON s.dept_id = d.dept_id;
```

Output:

| dept_name | name  |
| --------- | ----- |
| CSE       | Rahul |
| ECE       | Priya |
| CSE       | Arjun |
| EEE       | Sneha |
| CSE       | Kiran |

Now ask:

> **“Can you visually tell me how many students are in CSE?”**

Yes:

```text id="0c4i1d"
Rahul
Arjun
Kiran

= 3
```

But SQL needs to calculate it.

---

# 🔥 3. Introduce COUNT()

```sql id="y3dy8e"
SELECT COUNT(*)
FROM students;
```

This counts rows.

Result:

```text id="o0l7k9"
5
```

Now ask:

> **“Can I get the count separately for CSE, ECE and EEE?”**

That's where `GROUP BY` enters.

---

# 🧠 4. What Does GROUP BY Mean?

Tell them:

> **“GROUP BY puts similar rows into the same box.”**

Imagine:

```text id="l7tzqy"
CSE BOX
---------
Rahul
Arjun
Kiran

ECE BOX
---------
Priya

EEE BOX
---------
Sneha
```

Now SQL can count each box.

That's the mental model.

---

# 💻 5. GROUP BY

```sql id="iyc3pn"
SELECT dept_id, COUNT(*) AS student_count
FROM students
GROUP BY dept_id;
```

### Output

| dept_id | student_count |
| ------: | ------------: |
|      10 |             3 |
|      20 |             1 |
|      30 |             1 |

🔥 We grouped students by department.

---

# ⭐ The Golden Rule

Tell students:

> **“GROUP BY decides how the rows should be grouped. Aggregate functions calculate something inside each group.”**

Common aggregate functions:

```text id="3n7fla"
COUNT()
SUM()
AVG()
MIN()
MAX()
```

For now, focus heavily on `COUNT()`.

---

# 🔥 6. JOIN + GROUP BY

Now we don't want:

```text id="h2z2lf"
10
20
30
```

We want:

```text id="kw8y7u"
CSE
ECE
EEE
```

So JOIN the department table.

```sql id="j22l8v"
SELECT
    d.dept_name,
    COUNT(*) AS student_count
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
GROUP BY d.dept_name;
```

### Output

| dept_name | student_count |
| --------- | ------------: |
| CSE       |             3 |
| ECE       |             1 |
| EEE       |             1 |

---

# 🧠 7. Explain the Query Like a Story

Don't explain SQL syntax mechanically.

Walk through it:

### Step 1

```sql id="s6gl80"
FROM students
```

> Start with students.

### Step 2

```sql id="v0p34a"
JOIN departments
```

> Find department information.

### Step 3

```sql id="0tyz6q"
ON s.dept_id = d.dept_id
```

> Connect them.

### Step 4

```sql id="0b6cn5"
GROUP BY d.dept_name
```

> Put students into department boxes.

### Step 5

```sql id="o4a7sg"
COUNT(*)
```

> Count students inside each box.

That's the entire concept.

---

# 🎯 CASE 1 — How Many Students Per Department?

### Question

Find the number of students in each department.

### Answer

```sql id="3k8ihk"
SELECT
    d.dept_name,
    COUNT(*) AS total_students
FROM students s
JOIN departments d
ON s.dept_id = d.dept_id
GROUP BY d.dept_name;
```

### Output

| Department | Total Students |
| ---------- | -------------: |
| CSE        |              3 |
| ECE        |              1 |
| EEE        |              1 |

---

# 🔥 CASE 2 — Count Courses Per Student

Now use:

### `enrollments`

| student_id | course_id |
| ---------: | --------: |
|        101 |       501 |
|        101 |       502 |
|        102 |       503 |
|        103 |       501 |
|        103 |       502 |
|        103 |       503 |

Question:

> **“How many courses is each student taking?”**

First think:

```text id="9njg0w"
Student → Enrollment
```

### Answer

```sql id="8h7u2a"
SELECT
    s.name,
    COUNT(e.course_id) AS total_courses
FROM students s
JOIN enrollments e
ON s.student_id = e.student_id
GROUP BY s.name;
```

### Output

| Student | Total Courses |
| ------- | ------------: |
| Rahul   |             2 |
| Priya   |             1 |
| Arjun   |             3 |

---

# 🧠 CASE 3 — Why GROUP BY Is Necessary

Ask students:

> What happens if I write:

```sql id="q8lq7n"
SELECT s.name, COUNT(e.course_id)
FROM students s
JOIN enrollments e
ON s.student_id = e.student_id;
```

### ❌ Error in standard SQL

Why?

Because we selected:

```text id="qv1z8f"
s.name
```

but we're also using:

```text id="5s0n0b"
COUNT()
```

SQL needs to know:

> **“Count for whom?”**

So we say:

```sql id="wy1hve"
GROUP BY s.name
```

Now SQL knows:

> Count separately for each student.

---

# 🎯 CASE 4 — Find Number of Students Taking Each Course

### Courses

| course_id | course_name |
| --------: | ----------- |
|       501 | Python      |
|       502 | DBMS        |
|       503 | Java        |

Question:

> **“How many students are enrolled in each course?”**

Relationship:

```text id="z1i9h0"
Students
   ↓
Enrollments
   ↓
Courses
```

### Answer

```sql id="9x8i0j"
SELECT
    c.course_name,
    COUNT(e.student_id) AS total_students
FROM courses c
JOIN enrollments e
ON c.course_id = e.course_id
GROUP BY c.course_name;
```

### Output

| Course | Total Students |
| ------ | -------------: |
| Python |              2 |
| DBMS   |              2 |
| Java   |              1 |

---

# 🔥 CASE 5 — Find the Most Popular Course

Now make the question harder.

> **“Which course has the highest number of students?”**

First calculate:

```sql id="36m1fs"
SELECT
    c.course_name,
    COUNT(e.student_id) AS total_students
FROM courses c
JOIN enrollments e
ON c.course_id = e.course_id
GROUP BY c.course_name;
```

Then:

```text id="e7m1j3"
Python → 2
DBMS   → 2
Java   → 1
```

We can sort:

```sql id="wx6k47"
SELECT
    c.course_name,
    COUNT(e.student_id) AS total_students
FROM courses c
JOIN enrollments e
ON c.course_id = e.course_id
GROUP BY c.course_name
ORDER BY total_students DESC;
```

### Output

| Course | Students |
| ------ | -------: |
| Python |        2 |
| DBMS   |        2 |
| Java   |        1 |

Then later we can take the first row using `LIMIT`.

---

# 🧠 CASE 6 — GROUP BY + ORDER BY

Question:

> **“Show departments from highest student count to lowest.”**

### Answer

```sql id="n3gd3t"
SELECT
    d.dept_name,
    COUNT(*) AS total_students
FROM students s
JOIN departments d
ON s.dept_id = d.dept_id
GROUP BY d.dept_name
ORDER BY total_students DESC;
```

### Output

| Department | Students |
| ---------- | -------: |
| CSE        |        3 |
| ECE        |        1 |
| EEE        |        1 |

The mental flow:

```text id="3fn4qf"
JOIN
 ↓
GROUP BY
 ↓
COUNT
 ↓
ORDER BY
```

---

# 💥 CASE 7 — The Most Important New Question

Now ask:

> **“Suppose I only want departments having MORE THAN 2 students.”**

Students might say:

```sql id="qof6yb"
WHERE COUNT(*) > 2
```

### ❌ Wrong.

This is an important concept.

`WHERE` filters **rows before grouping**.

We need to filter **groups after grouping**.

That's where the next concept comes in:

# 👉 HAVING

---

# 🔥 8. Introduce HAVING

```sql id="y20qfp"
SELECT
    d.dept_name,
    COUNT(*) AS total_students
FROM students s
JOIN departments d
ON s.dept_id = d.dept_id
GROUP BY d.dept_name
HAVING COUNT(*) > 2;
```

### Output

| Department | Students |
| ---------- | -------: |
| CSE        |        3 |

---

# 🧠 WHERE vs HAVING

This distinction is **extremely important**.

### WHERE

Filters **individual rows**.

```sql id="8up7c8"
WHERE d.dept_name = 'CSE'
```

Meaning:

> Keep only CSE rows.

### HAVING

Filters **groups**.

```sql id="x02m3c"
HAVING COUNT(*) > 2
```

Meaning:

> Keep only groups whose count is greater than 2.

---

# ⭐ Memory Trick

Tell students:

> **WHERE asks: “Which rows?”**

> **HAVING asks: “Which groups?”**

That's one of the best beginner explanations.

---

# 🎯 CASE 8 — More Than One Course

Question:

> **“Find students enrolled in at least 2 courses.”**

### Answer

```sql id="k5kqyg"
SELECT
    s.name,
    COUNT(e.course_id) AS total_courses
FROM students s
JOIN enrollments e
ON s.student_id = e.student_id
GROUP BY s.name
HAVING COUNT(e.course_id) >= 2;
```

### Output

| Student | Courses |
| ------- | ------: |
| Rahul   |       2 |
| Arjun   |       3 |

---

# 🔥 CASE 9 — Department With At Least 2 Students

Question:

> Find departments containing at least 2 students.

### Answer

```sql id="bqcxj3"
SELECT
    d.dept_name,
    COUNT(*) AS total_students
FROM students s
JOIN departments d
ON s.dept_id = d.dept_id
GROUP BY d.dept_name
HAVING COUNT(*) >= 2;
```

### Output

| Department | Students |
| ---------- | -------: |
| CSE        |        3 |

---

# 🕵️ CASE 10 — Predict Before Running

### Students

| Name | Dept |
| ---- | ---: |
| A    |   10 |
| B    |   10 |
| C    |   20 |
| D    |   20 |
| E    |   20 |
| F    |   30 |

Query:

```sql id="t3gcne"
SELECT dept_id, COUNT(*) AS total
FROM students
GROUP BY dept_id
HAVING COUNT(*) > 1;
```

Ask:

> **“Which departments survive?”**

Calculate manually:

```text id="v5l4v1"
10 → 2 students ✅
20 → 3 students ✅
30 → 1 student  ❌
```

### Answer

| dept_id | total |
| ------: | ----: |
|      10 |     2 |
|      20 |     3 |

---

# 🚨 CASE 11 — The Classic WHERE/HAVING Trap

Question:

> Find departments with more than 2 students.

Wrong:

```sql id="qmt8mm"
WHERE COUNT(*) > 2
```

Correct:

```sql id="1kuyx1"
HAVING COUNT(*) > 2
```

Why?

Because:

```text id="v9y15r"
WHERE
 ↓
Rows

GROUP BY
 ↓
Groups

HAVING
 ↓
Groups after aggregation
```

---

# 🏆 FINAL SQL DETECTIVE CHALLENGE

Give them:

### Students

| ID | Name  | Dept |
| -: | ----- | ---: |
|  1 | Rahul |   10 |
|  2 | Priya |   20 |
|  3 | Arjun |   10 |
|  4 | Sneha |   10 |
|  5 | Kiran |   20 |
|  6 | Ravi  |   30 |

### Departments

| ID | Department |
| -: | ---------- |
| 10 | CSE        |
| 20 | ECE        |
| 30 | EEE        |

Question:

> **“Find departments having at least 2 students and show the result from highest student count to lowest.”**

### 🧠 Give them 3 minutes.

### ✅ Answer

```sql id="65vaxm"
SELECT
    d.dept_name,
    COUNT(*) AS total_students
FROM students s
JOIN departments d
ON s.dept_id = d.dept_id
GROUP BY d.dept_name
HAVING COUNT(*) >= 2
ORDER BY total_students DESC;
```

### Output

| Department | Students |
| ---------- | -------: |
| CSE        |        3 |
| ECE        |        2 |

---

# 🧠 The Complete Mental Model

Now show them:

```text id="b94y4f"
FROM
  ↓
JOIN
  ↓
ON
  ↓
WHERE
  ↓
GROUP BY
  ↓
COUNT / SUM / AVG / MAX / MIN
  ↓
HAVING
  ↓
ORDER BY
```

And the meaning:

```text id="grhphh"
JOIN       → Connect tables
WHERE      → Filter rows
GROUP BY   → Create groups
COUNT()    → Calculate
HAVING     → Filter groups
ORDER BY   → Sort
```

---

# 🎤 Exit Questions

Ask rapidly:

**“How many students are there?”**

👉 `COUNT(*)`

**“How many students in each department?”**

👉 `GROUP BY dept_id`

**“Only departments having more than 5 students?”**

👉 `HAVING COUNT(*) > 5`

**“Only CSE students?”**

👉 `WHERE dept_name = 'CSE'`

**“Sort highest to lowest?”**

👉 `ORDER BY ... DESC`

And finally:

> **“WHERE filters rows. HAVING filters groups.”**

Make them repeat that sentence.

---

## 🔜 NEXT TOPIC: AGGREGATE FUNCTIONS DEEP DIVE

We'll take **COUNT, SUM, AVG, MIN, MAX** one by one with a marks/salary/order dataset, and then solve tricky questions such as **average salary by department, highest marks in each department, departments whose average is above 80, and the difference between `COUNT(*)`, `COUNT(column)`, and `COUNT(DISTINCT column)`**.
