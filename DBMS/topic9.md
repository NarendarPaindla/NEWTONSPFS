# 🔥 TOPIC 8: JOIN + WHERE + ORDER BY

## “Now SQL Starts Answering Real Questions”

So far, students learned **how to connect tables**.

Now we teach them something more important:

> **“Once I connect the tables, how do I ask useful questions from the combined data?”**

We'll use the same college database so they don't have to learn new tables.

---

# 🎬 1. Start With a Real Question

Tell them:

> **“The Dean doesn't want the entire database. He asks me one specific question: Show me all CSE students.”**

We have:

### Students

| student_id | name  | dept_id |
| ---------: | ----- | ------: |
|        101 | Rahul |      10 |
|        102 | Priya |      20 |
|        103 | Arjun |      10 |
|        104 | Sneha |      30 |

### Departments

| dept_id | dept_name |
| ------: | --------- |
|      10 | CSE       |
|      20 | ECE       |
|      30 | EEE       |

---

# 🧠 2. First Connect the Tables

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

Now ask:

> **“Do I want all four students?”**

No.

> **“I only want CSE.”**

Now we need `WHERE`.

---

# 💻 3. JOIN + WHERE

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
WHERE d.dept_name = 'CSE';
```

### Output

| name  | dept_name |
| ----- | --------- |
| Rahul | CSE       |
| Arjun | CSE       |

---

# 🧠 The Critical Difference

Students often confuse:

```sql
ON
```

and

```sql
WHERE
```

Make this crystal clear.

### `ON`

> **How should two tables be connected?**

```sql
ON s.dept_id = d.dept_id
```

### `WHERE`

> **Which rows do I want after the relationship is established?**

```sql
WHERE d.dept_name = 'CSE'
```

Memory trick:

> 🔗 **ON = Connect**
> 🎯 **WHERE = Filter**

---

# 🔥 CASE 1 — Find ECE Students

### Question

> Find all students belonging to ECE.

### Answer

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
WHERE d.dept_name = 'ECE';
```

### Output

| name  | dept_name |
| ----- | --------- |
| Priya | ECE       |

---

# 🎯 CASE 2 — Find Students NOT From CSE

Question:

> Find students who are not from CSE.

### Answer

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
WHERE d.dept_name <> 'CSE';
```

### Output

| name  | dept_name |
| ----- | --------- |
| Priya | ECE       |
| Sneha | EEE       |

Explain:

```text
<> 
```

means:

> **not equal to**

---

# 🔥 Now Add a Third Table

### Courses

| course_id | course_name |
| --------: | ----------- |
|       501 | Python      |
|       502 | DBMS        |
|       503 | Java        |

### Enrollments

| student_id | course_id |
| ---------: | --------: |
|        101 |       501 |
|        101 |       502 |
|        102 |       503 |
|        103 |       501 |

---

# 🧠 CASE 3 — CSE Students Taking Python

This is a real SQL question.

Ask:

> **“Find students who belong to CSE AND are taking Python.”**

Think before SQL:

```text
Students
   ↓
Departments
   ↓
Enrollments
   ↓
Courses
```

We need four tables.

### Answer

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
WHERE d.dept_name = 'CSE'
AND c.course_name = 'Python';
```

### Output

| name  | dept_name | course_name |
| ----- | --------- | ----------- |
| Rahul | CSE       | Python      |
| Arjun | CSE       | Python      |

---

# 🧠 Break the WHERE Condition

We have:

```sql
WHERE d.dept_name = 'CSE'
AND c.course_name = 'Python';
```

This means:

```text
CSE
   AND
Python
```

Both conditions must be true.

---

# 🕵️ CASE 4 — OR

Question:

> Find students taking either Python OR Java.

### Answer

```sql
SELECT s.name, c.course_name
FROM students s
INNER JOIN enrollments e
ON s.student_id = e.student_id
INNER JOIN courses c
ON e.course_id = c.course_id
WHERE c.course_name = 'Python'
   OR c.course_name = 'Java';
```

### Output

| name  | course_name |
| ----- | ----------- |
| Rahul | Python      |
| Priya | Java        |
| Arjun | Python      |

---

# ⚠️ Very Important: AND vs OR

Tell students:

### AND

```text
A AND B
```

Both must be true.

### OR

```text
A OR B
```

At least one must be true.

Example:

> CSE **AND** Python

means:

```text
CSE ✅
Python ✅
```

Example:

> Python **OR** Java

means:

```text
Python ✅
OR
Java ✅
```

---

# 🔥 CASE 5 — Now Introduce ORDER BY

The Dean says:

> **“Show all students and their departments, but put the names in alphabetical order.”**

Query:

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
ORDER BY s.name;
```

### Output

| name  | dept_name |
| ----- | --------- |
| Arjun | CSE       |
| Priya | ECE       |
| Rahul | CSE       |
| Sneha | EEE       |

---

# 🧠 What Does ORDER BY Do?

`ORDER BY` means:

> **Sort the result.**

Default:

```sql
ORDER BY s.name ASC;
```

`ASC` means:

> Ascending.

For names:

```text
A → Z
```

---

# 🎯 CASE 6 — Descending

Question:

> Display students from Z → A.

### Answer

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
ORDER BY s.name DESC;
```

Output:

| name  | dept_name |
| ----- | --------- |
| Sneha | EEE       |
| Rahul | CSE       |
| Priya | ECE       |
| Arjun | CSE       |

---

# 🔥 CASE 7 — Filter + Sort

Question:

> **“Show only CSE students, sorted alphabetically.”**

### Answer

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id
WHERE d.dept_name = 'CSE'
ORDER BY s.name ASC;
```

### Output

| name  | dept_name |
| ----- | --------- |
| Arjun | CSE       |
| Rahul | CSE       |

Notice the flow:

```text
JOIN
 ↓
Connect tables

WHERE
 ↓
Filter rows

ORDER BY
 ↓
Sort result
```

---

# 🧠 CASE 8 — The SQL Pipeline

This is something I strongly recommend you teach visually.

Write:

```text
FROM
 ↓
JOIN
 ↓
ON
 ↓
WHERE
 ↓
SELECT
 ↓
ORDER BY
```

But **don't tell them this is exactly the physical execution order** yet. For beginners, use it as a **problem-solving workflow**.

Tell them:

> “When YOU write a query, think in this order.”

### 1️⃣ Which tables?

```text
FROM + JOIN
```

### 2️⃣ How are they connected?

```text
ON
```

### 3️⃣ Which rows do I need?

```text
WHERE
```

### 4️⃣ What should I display?

```text
SELECT
```

### 5️⃣ How should I arrange it?

```text
ORDER BY
```

This will help enormously when queries become complex.

---

# 💥 CASE 9 — Multiple Conditions

Question:

> Find CSE students taking Python or DBMS.

### Answer

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
WHERE d.dept_name = 'CSE'
AND (
    c.course_name = 'Python'
    OR c.course_name = 'DBMS'
)
ORDER BY s.name;
```

### Output

| name  | dept_name | course_name |
| ----- | --------- | ----------- |
| Arjun | CSE       | Python      |
| Rahul | CSE       | DBMS        |
| Rahul | CSE       | Python      |

---

# 🚨 Why did we use parentheses?

This is an important beginner trap.

We wrote:

```sql
AND (
    Python
    OR
    DBMS
)
```

We mean:

```text
CSE
AND
(Python OR DBMS)
```

Without parentheses, students can easily misunderstand how multiple conditions combine.

---

# 🎯 CASE 10 — The “Think Like SQL” Challenge

Give them:

> **Find all students taking Python and display them alphabetically.**

### Answer

```sql
SELECT
    s.name,
    c.course_name
FROM students s
INNER JOIN enrollments e
ON s.student_id = e.student_id
INNER JOIN courses c
ON e.course_id = c.course_id
WHERE c.course_name = 'Python'
ORDER BY s.name ASC;
```

### Output

| name  | course_name |
| ----- | ----------- |
| Arjun | Python      |
| Rahul | Python      |

---

# 🧩 CASE 11 — Use a Number in ORDER BY

Suppose:

```sql
SELECT
    s.name,
    d.dept_name,
    c.course_name
FROM students s
JOIN departments d
ON s.dept_id = d.dept_id
JOIN enrollments e
ON s.student_id = e.student_id
JOIN courses c
ON e.course_id = c.course_id;
```

We can sometimes write:

```sql
ORDER BY 1;
```

This means:

> Sort according to the first selected column.

If:

```text
SELECT s.name, d.dept_name
```

then:

```sql
ORDER BY 1;
```

means:

```text
ORDER BY s.name
```

However, for beginners, teach the explicit version:

```sql
ORDER BY s.name;
```

It's clearer and safer.

---

# 🏆 FINAL DETECTIVE CHALLENGE

Give them this question:

> **“Find all CSE students taking Python or DBMS and display them alphabetically.”**

Don't give the answer immediately.

Give them **3 minutes**.

Then reveal:

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
WHERE d.dept_name = 'CSE'
AND (
    c.course_name = 'Python'
    OR c.course_name = 'DBMS'
)
ORDER BY s.name ASC;
```

### Expected Output

| name  | dept_name | course_name |
| ----- | --------- | ----------- |
| Arjun | CSE       | Python      |
| Rahul | CSE       | Python      |
| Rahul | CSE       | DBMS        |

---

# 🧠 The Most Important Lesson Today

Give students this mental model:

```text
             SQL QUESTION
                  ↓
        Which tables do I need?
                  ↓
              JOIN
                  ↓
       How are they connected?
                  ↓
                ON
                  ↓
       Which rows do I want?
                  ↓
               WHERE
                  ↓
       How should they appear?
                  ↓
             ORDER BY
```

And these four words:

> 🔗 **JOIN = Connect**

> 🎯 **WHERE = Filter**

> 🔢 **ORDER BY = Sort**

> 👀 **SELECT = Display**

---

# 🎤 5-Minute Exit Quiz

Ask them verbally:

**1. `ON` is used for?**

👉 Connecting tables.

**2. `WHERE` is used for?**

👉 Filtering rows.

**3. `ORDER BY` is used for?**

👉 Sorting the result.

**4. `ASC` means?**

👉 Ascending.

**5. `DESC` means?**

👉 Descending.

**6. Want CSE students only?**

👉 `WHERE d.dept_name = 'CSE'`

**7. Want CSE students sorted by name?**

👉 Add `ORDER BY s.name`

**8. Want CSE AND Python?**

👉 Use `AND`.

**9. Want Python OR Java?**

👉 Use `OR`.

---

## 🔜 NEXT TOPIC: JOIN + GROUP BY

This is where the class becomes **much more powerful**.

We'll answer questions like:

* **How many students are in each department?**
* **How many students are taking each course?**
* **Which department has the most students?**
* **Which course has more than 2 students?**

This will naturally introduce **GROUP BY → aggregate functions → HAVING**, through problems rather than definitions.
