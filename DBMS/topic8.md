# 🔥 TOPIC 7: JOINING 3 OR MORE TABLES

## 🎬 Story: “The Complete Student Report”

Now students know how to join **two tables**.

Today, don't tell them:

> "We are learning multiple-table joins."

Tell them:

> **“Our college database has the information we need—but it is scattered across FOUR tables. The Dean wants one report. Your job is to connect the pieces.”**

This is where they start thinking like real SQL developers.

---

# 🧩 1. The Problem

We have four tables.

### 👨‍🎓 `students`

| student_id | name  | dept_id |
| ---------: | ----- | ------: |
|        101 | Rahul |      10 |
|        102 | Priya |      20 |
|        103 | Arjun |      10 |
|        104 | Sneha |      30 |

### 🏫 `departments`

| dept_id | dept_name |
| ------: | --------- |
|      10 | CSE       |
|      20 | ECE       |
|      30 | EEE       |

### 📚 `courses`

| course_id | course_name |
| --------: | ----------- |
|       501 | Python      |
|       502 | DBMS        |
|       503 | Java        |

### 📝 `enrollments`

| student_id | course_id |
| ---------: | --------: |
|        101 |       501 |
|        101 |       502 |
|        102 |       503 |
|        103 |       501 |

---

# 🎬 2. Give Students the Mission

Tell them:

> **“I want one report showing:”**

```text
Student Name
Department
Course
```

Ask:

> **“Can we get all three pieces from `students`?”**

No.

Ask:

> **“Where do we get department?”**

`departments`

Ask:

> **“Where do we get course?”**

`courses`

Ask:

> **“But how does a student connect to a course?”**

Through:

```text
enrollments
```

🔥 Now draw:

```text
STUDENTS
   |
   | dept_id
   ↓
DEPARTMENTS


STUDENTS
   |
   ↓
ENROLLMENTS
   |
   | course_id
   ↓
COURSES
```

This diagram is the **most important part**.

---

# 🧠 3. Don't Jump to SQL

Ask students:

> **“What is the first connection we can make?”**

Students → Departments.

```text
students.dept_id
       =
departments.dept_id
```

Then:

> **“How does student connect to enrollment?”**

```text
students.student_id
       =
enrollments.student_id
```

Then:

> **“How does enrollment connect to course?”**

```text
enrollments.course_id
       =
courses.course_id
```

Now the entire path becomes:

```text
Student
   ↓
Enrollment
   ↓
Course
```

and separately:

```text
Student
   ↓
Department
```

---

# 💻 4. First Join: Students + Departments

Start small:

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id;
```

Output:

| name  | dept_name |
| ----- | --------- |
| Rahul | CSE       |
| Priya | ECE       |
| Arjun | CSE       |
| Sneha | EEE       |

Tell them:

> **“We have solved only half of the problem.”**

---

# 🔥 5. Add the Third Table

Now connect `enrollments`.

```sql
SELECT
    s.name,
    d.dept_name,
    e.course_id
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
INNER JOIN enrollments e
ON s.student_id = e.student_id;
```

Now we have:

| name  | dept_name | course_id |
| ----- | --------- | --------: |
| Rahul | CSE       |       501 |
| Rahul | CSE       |       502 |
| Priya | ECE       |       503 |
| Arjun | CSE       |       501 |

Notice something interesting.

Rahul appears **twice**.

Ask:

> **“Did SQL duplicate Rahul by mistake?”**

### ❌ No.

Rahul has two enrollments:

```text
Rahul → Python
Rahul → DBMS
```

Therefore two rows are correct.

---

# 🧠 6. Add the Fourth Table

Now replace `course_id` with the actual course name.

```sql
SELECT
    s.name,
    d.dept_name,
    c.course_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
INNER JOIN enrollments e
ON s.student_id = e.student_id
INNER JOIN courses c
ON e.course_id = c.course_id;
```

### Output

| name  | dept_name | course_name |
| ----- | --------- | ----------- |
| Rahul | CSE       | Python      |
| Rahul | CSE       | DBMS        |
| Priya | ECE       | Java        |
| Arjun | CSE       | Python      |

🔥 This is a **real-world multi-table JOIN**.

---

# ⭐ Golden Rule

Tell students:

> **“When joining 3 or 4 tables, don't think about the whole query at once. Follow the relationship path one table at a time.”**

Think:

```text
A → B → C → D
```

Not:

```text
A + B + C + D 😵
```

---

# 🎯 CASE 1 — 3 Tables

### `students`

| ID | Name |
| -: | ---- |
|  1 | A    |
|  2 | B    |
|  3 | C    |

### `enrollments`

| Student ID | Course ID |
| ---------: | --------: |
|          1 |       101 |
|          1 |       102 |
|          2 |       101 |

### `courses`

| Course ID | Course |
| --------: | ------ |
|       101 | Python |
|       102 | SQL    |
|       103 | Java   |

### Question

> Find every student and the courses they enrolled in.

### 🧠 First think about relationships

```text
students
   ↓
enrollments
   ↓
courses
```

### ✅ Answer

```sql
SELECT
    s.name,
    c.course
FROM students s
INNER JOIN enrollments e
ON s.id = e.student_id
INNER JOIN courses c
ON e.course_id = c.course_id;
```

### Output

| name | course |
| ---- | ------ |
| A    | Python |
| A    | SQL    |
| B    | Python |

Why doesn't C appear?

C has no enrollment.

---

# 🔥 CASE 2 — Find Students With Their Departments AND Courses

Use our original data.

### Question

> Display:

```text
Student
Department
Course
```

### ✅ Answer

```sql
SELECT
    s.name,
    d.dept_name,
    c.course_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
INNER JOIN enrollments e
ON s.student_id = e.student_id
INNER JOIN courses c
ON e.course_id = c.course_id;
```

### Output

| Student | Department | Course |
| ------- | ---------- | ------ |
| Rahul   | CSE        | Python |
| Rahul   | CSE        | DBMS   |
| Priya   | ECE        | Java   |
| Arjun   | CSE        | Python |

---

# 🕵️ CASE 3 — Find All Students, Even Those Without Courses

Now introduce a very important real-world problem.

> **“The Dean wants EVERY student. Students who haven't enrolled in any course should also appear.”**

Our previous query used:

```text
INNER JOIN enrollments
```

That would remove students without enrollment.

So change that JOIN:

```sql
SELECT
    s.name,
    d.dept_name,
    c.course_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
LEFT JOIN enrollments e
ON s.student_id = e.student_id
LEFT JOIN courses c
ON e.course_id = c.course_id;
```

### Suppose Sneha has no enrollment.

Output:

| Student | Department | Course |
| ------- | ---------- | ------ |
| Rahul   | CSE        | Python |
| Rahul   | CSE        | DBMS   |
| Priya   | ECE        | Java   |
| Arjun   | CSE        | Python |
| Sneha   | EEE        | NULL   |

🔥 This demonstrates that **JOIN type can change at every step**.

---

# 🧠 Important Concept

Students often think:

> "If I start with LEFT JOIN, everything will remain."

Not necessarily.

The important question is:

> **“Which table's rows do I want to preserve at that particular relationship?”**

For example:

```sql
students
LEFT JOIN enrollments
```

means:

> Keep all students.

---

# 🎯 CASE 4 — Find Students With No Courses

Using the same query:

```sql
SELECT
    s.name
FROM students s
LEFT JOIN enrollments e
ON s.student_id = e.student_id
WHERE e.student_id IS NULL;
```

### Answer

If Sneha has no enrollment:

```text
Sneha
```

This is the same powerful pattern you learned earlier:

```text
LEFT JOIN
+
WHERE right_table.id IS NULL
```

---

# 💥 CASE 5 — Find CSE Students and Their Courses

Question:

> **“Show only CSE students and the courses they enrolled in.”**

### Step 1: Connect the tables.

### Step 2: Filter department.

### Answer:

```sql
SELECT
    s.name,
    d.dept_name,
    c.course_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
INNER JOIN enrollments e
ON s.student_id = e.student_id
INNER JOIN courses c
ON e.course_id = c.course_id
WHERE d.dept_name = 'CSE';
```

### Output

| name  | dept_name | course_name |
| ----- | --------- | ----------- |
| Rahul | CSE       | Python      |
| Rahul | CSE       | DBMS        |
| Arjun | CSE       | Python      |

---

# 🔥 CASE 6 — Find Who Is Taking Python

Question:

> **“Which students are enrolled in Python?”**

### Answer

```sql
SELECT s.name
FROM students s
INNER JOIN enrollments e
ON s.student_id = e.student_id
INNER JOIN courses c
ON e.course_id = c.course_id
WHERE c.course_name = 'Python';
```

### Output

| name  |
| ----- |
| Rahul |
| Arjun |

---

# 🧠 CASE 7 — Predict Before Running

Give students:

### Students

| ID | Name |
| -: | ---- |
|  1 | A    |
|  2 | B    |
|  3 | C    |

### Enrollments

| Student | Course |
| ------: | -----: |
|       1 |    101 |
|       1 |    102 |
|       2 |    101 |

### Courses

|  ID | Name   |
| --: | ------ |
| 101 | Python |
| 102 | SQL    |

Query:

```sql
SELECT s.name, c.name
FROM students s
JOIN enrollments e
ON s.id = e.student_id
JOIN courses c
ON e.course_id = c.id;
```

Ask:

> **“How many rows?”**

### Answer: 3

```text
A → Python
A → SQL
B → Python
```

C disappears because C has no enrollment.

---

# 🚨 CASE 8 — The Common Mistake

Students may write:

```sql
SELECT s.name, c.course_name
FROM students s
JOIN courses c
ON s.student_id = c.course_id;
```

Ask:

> **“Why is this wrong?”**

Because there is **no direct relationship** between these tables.

The relationship is:

```text
students
   ↓
enrollments
   ↓
courses
```

So we must go through `enrollments`.

Correct:

```sql
SELECT s.name, c.course_name
FROM students s
JOIN enrollments e
ON s.student_id = e.student_id
JOIN courses c
ON e.course_id = c.course_id;
```

🔥 Tell them:

> **“Don't JOIN tables because you see two tables. JOIN them because you understand their relationship.”**

---

# 🏆 FINAL CHALLENGE

Give them this:

### `students`

| ID | Name  | Dept |
| -: | ----- | ---: |
|  1 | Rahul |   10 |
|  2 | Priya |   20 |
|  3 | Arjun |   10 |

### `departments`

| ID | Department |
| -: | ---------- |
| 10 | CSE        |
| 20 | ECE        |

### `enrollments`

| Student | Course |
| ------: | -----: |
|       1 |    101 |
|       1 |    102 |
|       2 |    101 |

### `courses`

|  ID | Course |
| --: | ------ |
| 101 | Python |
| 102 | DBMS   |
| 103 | Java   |

---

## Challenge A

> Find student name + course name.

### Answer

```sql
SELECT s.name, c.course
FROM students s
JOIN enrollments e
ON s.id = e.student
JOIN courses c
ON e.course = c.id;
```

### Output

| Name  | Course |
| ----- | ------ |
| Rahul | Python |
| Rahul | DBMS   |
| Priya | Python |

---

## Challenge B

> Find CSE students and their courses.

### Answer

```sql
SELECT s.name, c.course
FROM students s
JOIN departments d
ON s.dept = d.id
JOIN enrollments e
ON s.id = e.student
JOIN courses c
ON e.course = c.id
WHERE d.department = 'CSE';
```

### Output

| Name  | Course |
| ----- | ------ |
| Rahul | Python |
| Rahul | DBMS   |

---

## Challenge C

> Find students who are NOT enrolled in any course.

### Answer

```sql
SELECT s.name
FROM students s
LEFT JOIN enrollments e
ON s.id = e.student
WHERE e.student IS NULL;
```

### Output

| Name  |
| ----- |
| Arjun |

---

# 🧠 The Most Important Lesson

When you see a complicated query like:

```sql
SELECT ...
FROM A
JOIN B
ON ...
JOIN C
ON ...
JOIN D
ON ...
```

**Don't panic.**

Break it into:

```text
A → B
B → C
C → D
```

Then solve one relationship at a time.

### Your students should remember:

> **“Multiple-table JOIN is not a new JOIN type. It is simply the same JOIN idea repeated across multiple relationships.”**

---

# 🎤 30-Second Exit Test

Ask them these without SQL:

**Student → Department?**

`dept_id`

**Student → Course?**

Through `enrollments`

**Enrollment → Course?**

`course_id`

**Need only matching records?**

`INNER JOIN`

**Need every student?**

`LEFT JOIN`

**Need to connect 4 tables?**

> **Follow the relationship path one JOIN at a time.**

---

## 🔜 NEXT TOPIC: JOIN + WHERE + ORDER BY

We'll take the multi-table data and turn it into **real questions like “Top students in CSE taking Python,” “sort students by course,” “find students from a particular department,”** etc. This is where students start writing SQL that looks like actual interview/company queries.
