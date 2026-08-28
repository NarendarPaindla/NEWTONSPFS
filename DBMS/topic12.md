# 🔥 TOPIC 11: SUBQUERIES — “SQL INSIDE SQL”

Now we enter one of the most important parts of SQL.

Don't start by saying:

> “A subquery is a query inside another query.”

That's technically correct—but boring.

Instead, tell them:

> **“Imagine I ask you: Who scored above the class average?”**

A student cannot answer immediately.

First they need to find:

```text
Class Average
```

Then compare every student's marks against it.

That's exactly what a **subquery** does.

---

# 🎬 1. The Detective Story

### `students`

| id | name  | marks |
| -: | ----- | ----: |
|  1 | Rahul |    85 |
|  2 | Priya |    72 |
|  3 | Arjun |    95 |
|  4 | Sneha |    68 |
|  5 | Kiran |    78 |

Question:

> 🔎 **“Who scored above the class average?”**

First calculate average:

```sql
SELECT AVG(marks)
FROM students;
```

Result:

```text
79.6
```

Now we need:

```text
85 > 79.6 ✅
72 > 79.6 ❌
95 > 79.6 ✅
68 > 79.6 ❌
78 > 79.6 ❌
```

Answer:

```text
Rahul
Arjun
```

But instead of manually finding `79.6`, let SQL find it.

---

# 🧠 2. The Subquery

```sql
SELECT name, marks
FROM students
WHERE marks > (
    SELECT AVG(marks)
    FROM students
);
```

Look at the structure:

```text id="5d4a6k"
OUTER QUERY
     ↓
Find students
     ↓
WHERE marks >
     ↓
SUBQUERY
     ↓
Find average marks
```

---

# ⭐ Golden Rule

Tell your students:

> **“The inner query finds the information. The outer query uses that information.”**

That's the core idea.

---

# 🧩 3. Understand the Query Step-by-Step

### Inner query:

```sql
SELECT AVG(marks)
FROM students;
```

Produces:

```text
79.6
```

Then SQL effectively sees:

```sql
SELECT name, marks
FROM students
WHERE marks > 79.6;
```

Output:

| name  | marks |
| ----- | ----: |
| Rahul |    85 |
| Arjun |    95 |

🔥 This is the easiest way to explain subqueries.

---

# 🎯 CASE 1 — Above Average

Question:

> Find students whose marks are greater than the average marks.

### Answer

```sql
SELECT
    name,
    marks
FROM students
WHERE marks > (
    SELECT AVG(marks)
    FROM students
);
```

### Output

| Name  | Marks |
| ----- | ----: |
| Rahul |    85 |
| Arjun |    95 |

---

# 🧠 CASE 2 — Below Average

Question:

> Find students who scored below the class average.

### Answer

```sql
SELECT
    name,
    marks
FROM students
WHERE marks < (
    SELECT AVG(marks)
    FROM students
);
```

### Output

| Name  | Marks |
| ----- | ----: |
| Priya |    72 |
| Sneha |    68 |
| Kiran |    78 |

---

# 🔥 CASE 3 — Find the Top Scorer

Question:

> Find the student who scored the highest marks.

First:

```sql
SELECT MAX(marks)
FROM students;
```

Result:

```text
95
```

Now use it:

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MAX(marks)
    FROM students
);
```

### Output

| Name  | Marks |
| ----- | ----: |
| Arjun |    95 |

---

# 🎯 CASE 4 — Find the Lowest Scorer

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MIN(marks)
    FROM students
);
```

### Output

| Name  | Marks |
| ----- | ----: |
| Sneha |    68 |

---

# 🧠 4. Why Not Just Use MAX()?

Ask:

> “Why can't I simply write?”

```sql
SELECT name, MAX(marks)
FROM students;
```

Because you're trying to return:

```text
name
+
aggregate result
```

without grouping appropriately.

The subquery solves the problem by first finding the value:

```text
95
```

and then finding the student having that value.

---

# 🔥 CASE 5 — Find Students Who Got the Maximum

This is an important pattern:

```text id="m2w7p6"
WHERE column = (
    SELECT MAX(column)
    FROM table
)
```

Example:

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MAX(marks)
    FROM students
);
```

Tell students:

> **“Whenever you need the actual row corresponding to MAX or MIN, this pattern is extremely useful.”**

---

# 🎬 5. Subquery With Another Table

Now bring back departments.

### `students`

| id | name  | dept_id | marks |
| -: | ----- | ------: | ----: |
|  1 | Rahul |      10 |    85 |
|  2 | Priya |      20 |    72 |
|  3 | Arjun |      10 |    95 |
|  4 | Sneha |      30 |    68 |
|  5 | Kiran |      10 |    78 |

### `departments`

| dept_id | dept_name |
| ------: | --------- |
|      10 | CSE       |
|      20 | ECE       |
|      30 | EEE       |

Question:

> **“Find students who belong to the CSE department.”**

First find CSE's ID:

```sql
SELECT dept_id
FROM departments
WHERE dept_name = 'CSE';
```

Result:

```text
10
```

Now use that result:

```sql
SELECT name
FROM students
WHERE dept_id = (
    SELECT dept_id
    FROM departments
    WHERE dept_name = 'CSE'
);
```

### Output

| name  |
| ----- |
| Rahul |
| Arjun |
| Kiran |

---

# 🧠 The Pattern

```text id="a8s8h5"
Outer Query
      ↓
WHERE dept_id =
      ↓
Inner Query
      ↓
Find CSE dept_id
```

This is a **single-row subquery** because the inner query returns one value:

```text
10
```

---

# 🚨 CASE 6 — What If the Subquery Returns Multiple Rows?

This is where students need to understand why `=` sometimes fails.

Suppose:

### Departments

| dept_id | dept_name |
| ------: | --------- |
|      10 | CSE       |
|      20 | ECE       |
|      30 | CSE       |

Now:

```sql
SELECT dept_id
FROM departments
WHERE dept_name = 'CSE';
```

returns:

```text
10
20? 
```

Actually:

```text
10
30
```

That's **two rows**.

Now this is problematic:

```sql
WHERE dept_id = (
    SELECT dept_id
    FROM departments
    WHERE dept_name = 'CSE'
);
```

Because:

```text
=
```

expects one value.

But the subquery gives:

```text
10
30
```

🔥 This is when we introduce:

# 👉 `IN`

---

# 🎯 CASE 7 — IN + Subquery

Question:

> Find students belonging to departments named CSE.

If multiple CSE department IDs can exist:

```sql
SELECT name
FROM students
WHERE dept_id IN (
    SELECT dept_id
    FROM departments
    WHERE dept_name = 'CSE'
);
```

`IN` means:

> **“Does this value exist in the list?”**

For example:

```text
10 IN (10, 30) → TRUE
20 IN (10, 30) → FALSE
30 IN (10, 30) → TRUE
```

---

# ⭐ Very Important Difference

### `=`

```sql
WHERE dept_id = (
    SELECT ...
)
```

Means:

> The subquery should produce **one value**.

### `IN`

```sql
WHERE dept_id IN (
    SELECT ...
)
```

Means:

> The subquery can produce **multiple values**.

---

# 🔥 CASE 8 — Students Enrolled in Python

### `courses`

| course_id | course_name |
| --------: | ----------- |
|       501 | Python      |
|       502 | DBMS        |
|       503 | Java        |

### `enrollments`

| student_id | course_id |
| ---------: | --------: |
|        101 |       501 |
|        101 |       502 |
|        102 |       503 |
|        103 |       501 |

Question:

> Find students enrolled in Python.

First:

```sql
SELECT course_id
FROM courses
WHERE course_name = 'Python';
```

Result:

```text
501
```

Then:

```sql
SELECT student_id
FROM enrollments
WHERE course_id = (
    SELECT course_id
    FROM courses
    WHERE course_name = 'Python'
);
```

Result:

```text
101
103
```

Now get names:

```sql
SELECT name
FROM students
WHERE student_id IN (
    SELECT student_id
    FROM enrollments
    WHERE course_id = (
        SELECT course_id
        FROM courses
        WHERE course_name = 'Python'
    )
);
```

### Output

| name  |
| ----- |
| Rahul |
| Arjun |

🔥 Notice:

We just created a **subquery inside a subquery**.

---

# 🧠 CASE 9 — Nested Subquery Challenge

Question:

> Find students who are enrolled in DBMS.

### Answer

```sql
SELECT name
FROM students
WHERE student_id IN (
    SELECT student_id
    FROM enrollments
    WHERE course_id = (
        SELECT course_id
        FROM courses
        WHERE course_name = 'DBMS'
    )
);
```

### Expected Output

| Name  |
| ----- |
| Rahul |

---

# 🎯 CASE 10 — Above CSE Average

Now we're going to make it more interesting.

Question:

> **“Find students whose marks are above the average marks of CSE students.”**

First find CSE average:

```sql
SELECT AVG(marks)
FROM students
WHERE dept_id = 10;
```

CSE marks:

```text
85, 95, 78
```

Average:

```text
86
```

Now:

```sql
SELECT name, marks
FROM students
WHERE marks > (
    SELECT AVG(marks)
    FROM students
    WHERE dept_id = 10
);
```

### Output

| Name  | Marks |
| ----- | ----: |
| Arjun |    95 |

🔥 Notice something powerful:

The outer query examines **all students**, but the subquery calculates the benchmark only from **CSE students**.

---

# 💥 CASE 11 — Second Highest Marks

This is a classic interview question.

Data:

```text
95
85
78
72
68
```

Highest:

```sql
SELECT MAX(marks)
FROM students;
```

= `95`

Now find the maximum value **less than 95**.

```sql
SELECT MAX(marks)
FROM students
WHERE marks < (
    SELECT MAX(marks)
    FROM students
);
```

Result:

```text
85
```

Now find the student:

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MAX(marks)
    FROM students
    WHERE marks < (
        SELECT MAX(marks)
        FROM students
    )
);
```

### Output

| Name  | Marks |
| ----- | ----: |
| Rahul |    85 |

🔥 This is an excellent challenge to give your students.

---

# 🧠 CASE 12 — Third Highest

Don't immediately give the query.

Ask students:

> “If we can find the second highest, how could we find the third highest?”

Let them reason.

Conceptually:

```text id="p9oqrj"
Find highest
     ↓
Exclude highest
     ↓
Find highest again
     ↓
Exclude second highest
     ↓
Find highest again
```

For beginners, don't spend too much time on the giant nested version yet. The important thing is understanding the **subquery thinking pattern**.

---

# 🏆 FINAL DETECTIVE CHALLENGE

Give students:

### `students`

| Name  | Marks |
| ----- | ----: |
| Rahul |    85 |
| Priya |    72 |
| Arjun |    95 |
| Sneha |    68 |
| Kiran |    78 |

Ask:

### Q1

Find students above average.

**Answer:**

```sql
SELECT name, marks
FROM students
WHERE marks > (
    SELECT AVG(marks)
    FROM students
);
```

Result:

```text
Rahul
Arjun
```

---

### Q2

Find the highest scorer.

**Answer:**

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MAX(marks)
    FROM students
);
```

Result:

```text
Arjun — 95
```

---

### Q3

Find the lowest scorer.

**Answer:**

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MIN(marks)
    FROM students
);
```

Result:

```text
Sneha — 68
```

---

### Q4

Find the second-highest scorer.

**Answer:**

```sql
SELECT name, marks
FROM students
WHERE marks = (
    SELECT MAX(marks)
    FROM students
    WHERE marks < (
        SELECT MAX(marks)
        FROM students
    )
);
```

Result:

```text
Rahul — 85
```

---

# 🧠 The Subquery Mental Model

Make students remember this:

```text id="9f6q9a"
        OUTER QUERY
             ↓
       "I need an answer"
             ↓
       INNER QUERY
             ↓
     "Let me calculate it"
             ↓
       Result returned
             ↓
       Outer query uses it
```

### Three patterns they've learned:

**Pattern 1 — Compare with one value**

```sql
WHERE marks > (
    SELECT AVG(marks)
    FROM students
)
```

**Pattern 2 — Compare with multiple values**

```sql
WHERE dept_id IN (
    SELECT dept_id
    FROM departments
)
```

**Pattern 3 — Nested subqueries**

```text
Query A
 ↓
Query B
 ↓
Query C
```

---

# 🎤 Exit Challenge

Don't show the answer immediately.

Ask:

> **“Find students whose marks are greater than the average marks of ECE students.”**

Give them 3 minutes.

### ✅ Answer

```sql
SELECT name, marks
FROM students
WHERE marks > (
    SELECT AVG(marks)
    FROM students
    WHERE dept_id = (
        SELECT dept_id
        FROM departments
        WHERE dept_name = 'ECE'
    )
);
```

### How to think:

```text
Who are we looking for?
        ↓
Students
        ↓
What should they beat?
        ↓
ECE average
        ↓
Which department is ECE?
        ↓
ECE department ID
```

That is the **real skill behind subqueries**.

---

# 🔜 NEXT TOPIC: `IN`, `NOT IN`, `ANY`, `ALL`

This is where subqueries become much more interesting.

We'll use a **“Student vs Department Marks Challenge”** to make students understand the difference between:

```text
IN       → matches any value in a list
NOT IN   → matches none
ANY      → comparison with at least one value
ALL      → comparison with every value
```

with prediction games and tricky cases.
