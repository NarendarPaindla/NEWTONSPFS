# 🔥 TOPIC 6: CROSS JOIN — “The Combination Machine”

This is a great topic to make interesting because **CROSS JOIN behaves very differently from the JOINs students just learned**.

Don't start with syntax.

Start with a problem.

---

# 🎬 1. The College Fest Story

Tell your students:

> **“BITS Pilani is organizing a college fest. I have 3 T-shirts and 2 sizes. I want to know every possible T-shirt–size combination.”**

### 👕 T-Shirts

| shirt_id | color |
| -------: | ----- |
|        1 | Black |
|        2 | White |
|        3 | Blue  |

### 📏 Sizes

| size_id | size |
| ------: | ---- |
|       1 | S    |
|       2 | M    |

Ask:

> **“How many possible combinations can we create?”**

Let them guess.

Then manually calculate:

```text
Black + S
Black + M

White + S
White + M

Blue + S
Blue + M
```

### Answer:

**6 combinations**

---

# 🧠 2. Notice Something Important

With INNER/LEFT/RIGHT JOIN, we had a **matching condition**:

```sql
ON s.dept_id = d.dept_id
```

But here?

We don't want matching.

We want:

> **EVERY possible combination.**

That's exactly what `CROSS JOIN` does.

---

# 💻 3. CROSS JOIN

```sql
SELECT t.color, s.size
FROM tshirts t
CROSS JOIN sizes s;
```

### Output

| color | size |
| ----- | ---- |
| Black | S    |
| Black | M    |
| White | S    |
| White | M    |
| Blue  | S    |
| Blue  | M    |

---

# ⭐ The Golden Rule

Put this on screen:

```text
CROSS JOIN
     ↓
EVERY ROW
FROM TABLE 1
     ×
EVERY ROW
FROM TABLE 2
```

Or tell them:

> **“CROSS JOIN doesn't ask ‘Do these rows match?’ It says ‘Give me every possible combination.’”**

---

# 💥 4. The Most Important Formula

If:

```text
Table A = M rows
Table B = N rows
```

Then:

```text
CROSS JOIN result = M × N
```

Example:

```text
3 shirts × 2 sizes
= 6 combinations
```

Another:

```text
5 students × 4 courses
= 20 combinations
```

Another:

```text
100 products × 10 colors
= 1,000 combinations
```

This is the first thing students should learn.

---

# 🎯 CASE 1 — Predict Before Running

### `colors`

| color |
| ----- |
| Red   |
| Blue  |
| Green |

### `sizes`

| size |
| ---- |
| S    |
| M    |
| L    |
| XL   |

Query:

```sql
SELECT c.color, s.size
FROM colors c
CROSS JOIN sizes s;
```

### 🛑 Ask students:

> **“How many rows?”**

Don't let them run it.

Calculate:

```text
3 × 4 = 12
```

### ✅ Answer: 12 rows

First few:

| color | size |
| ----- | ---- |
| Red   | S    |
| Red   | M    |
| Red   | L    |
| Red   | XL   |
| Blue  | S    |
| Blue  | M    |
| Blue  | L    |
| Blue  | XL   |
| Green | S    |
| Green | M    |
| Green | L    |
| Green | XL   |

---

# 🧩 CASE 2 — College Courses

Suppose the college has:

### Departments

| Department |
| ---------- |
| CSE        |
| ECE        |
| EEE        |

### Semesters

| Semester |
| -------- |
| 1-1      |
| 1-2      |

Question:

> **“Generate every department-semester combination.”**

### Answer

```sql
SELECT d.department, s.semester
FROM departments d
CROSS JOIN semesters s;
```

### Output

| department | semester |
| ---------- | -------- |
| CSE        | 1-1      |
| CSE        | 1-2      |
| ECE        | 1-1      |
| ECE        | 1-2      |
| EEE        | 1-1      |
| EEE        | 1-2      |

**3 × 2 = 6 rows**

---

# 🔥 CASE 3 — Timetable Generator

Now make it more realistic.

### Subjects

| Subject     |
| ----------- |
| Python      |
| DBMS        |
| Mathematics |

### Time Slots

| Slot  |
| ----- |
| 9 AM  |
| 11 AM |
| 2 PM  |

Question:

> **“Generate every possible subject-time combination.”**

### Answer

```sql
SELECT s.subject, t.slot
FROM subjects s
CROSS JOIN time_slots t;
```

### Output

| subject     | slot  |
| ----------- | ----- |
| Python      | 9 AM  |
| Python      | 11 AM |
| Python      | 2 PM  |
| DBMS        | 9 AM  |
| DBMS        | 11 AM |
| DBMS        | 2 PM  |
| Mathematics | 9 AM  |
| Mathematics | 11 AM |
| Mathematics | 2 PM  |

**3 × 3 = 9 combinations**

Then tell students:

> “This doesn't mean these are valid timetables. It means SQL generated the **possible combinations**. We can later filter invalid ones.”

That's an important distinction.

---

# 🧠 CASE 4 — The Dangerous CROSS JOIN

Now show:

```text
Students = 1,000
Courses = 50
```

Ask:

> **“If I CROSS JOIN them, how many rows?”**

Calculate:

```text
1,000 × 50
= 50,000
```

Then:

```text
10,000 students × 100 courses
= 1,000,000 rows
```

🔥 Tell them:

> **“CROSS JOIN is powerful because it creates combinations. It's dangerous because the number of combinations can explode.”**

This is a very useful real-world lesson.

---

# 🚨 CASE 5 — Find the Mistake

Suppose a student writes:

```sql
SELECT s.name, d.dept_name
FROM students s
CROSS JOIN departments d;
```

They wanted:

> Students with their actual department.

Ask:

> **“Is this correct?”**

### ❌ No.

Why?

Because CROSS JOIN doesn't match:

```text
student.dept_id
       =
department.dept_id
```

Instead, it creates:

```text
Every student × Every department
```

If there are:

```text
100 students
10 departments
```

the query gives:

**1,000 rows.**

Instead, they need:

```sql
SELECT s.name, d.dept_name
FROM students s
INNER JOIN departments d
ON s.dept_id = d.dept_id;
```

---

# 🎯 CASE 6 — Predict the Explosion

### Students

| Name |
| ---- |
| A    |
| B    |
| C    |
| D    |

### Clubs

| Club     |
| -------- |
| Coding   |
| Robotics |
| Music    |

Question:

```sql
SELECT s.name, c.club
FROM students s
CROSS JOIN clubs c;
```

### How many rows?

```text
4 × 3 = 12
```

### Answer: 12

For student A:

```text
A → Coding
A → Robotics
A → Music
```

For B:

```text
B → Coding
B → Robotics
B → Music
```

And so on.

---

# 🕵️ CASE 7 — Real SQL Problem

A college wants to create a list of:

* 4 classrooms
* 3 exam sessions

They want **every possible classroom-session combination**.

### Tables

`classrooms`

| room |
| ---- |
| A101 |
| A102 |
| B201 |
| B202 |

`sessions`

| session   |
| --------- |
| Morning   |
| Afternoon |
| Evening   |

Question:

> Generate all possible combinations.

### Answer

```sql
SELECT r.room, s.session
FROM classrooms r
CROSS JOIN sessions s;
```

### Number of combinations

```text
4 × 3 = 12
```

---

# 💣 CASE 8 — Can CROSS JOIN Have a WHERE?

Yes.

This is where you can show students that CROSS JOIN can be used to **generate possibilities and then filter them**.

Suppose:

### Products

| Product |
| ------- |
| Laptop  |
| Phone   |
| Tablet  |

### Colors

| Color |
| ----- |
| Black |
| White |

Generate combinations but only keep products that are not tablets:

```sql
SELECT p.product, c.color
FROM products p
CROSS JOIN colors c
WHERE p.product <> 'Tablet';
```

### Output

| Product | Color |
| ------- | ----- |
| Laptop  | Black |
| Laptop  | White |
| Phone   | Black |
| Phone   | White |

---

# 🧠 CROSS JOIN vs Other JOINs

Now pause and compare everything they've learned.

| JOIN            | Main idea                  |
| --------------- | -------------------------- |
| INNER JOIN      | Only matching rows         |
| LEFT JOIN       | Everything from LEFT       |
| RIGHT JOIN      | Everything from RIGHT      |
| FULL OUTER JOIN | Everything from BOTH       |
| CROSS JOIN      | Every possible combination |

The key question:

### INNER

> **“Do they match?”**

### LEFT

> **“Keep everyone from the left.”**

### RIGHT

> **“Keep everyone from the right.”**

### FULL

> **“Keep everyone.”**

### CROSS

> **“What are all possible combinations?”**

---

# 🏆 FINAL CROSS JOIN CHALLENGE

Give students:

### `students`

| Name  |
| ----- |
| Rahul |
| Priya |
| Arjun |

### `subjects`

| Subject |
| ------- |
| Python  |
| SQL     |

Ask:

### Q1

How many rows?

**Answer:**

```text
3 × 2 = 6
```

### Q2

Write the query.

**Answer:**

```sql
SELECT s.name, sub.subject
FROM students s
CROSS JOIN subjects sub;
```

### Q3

Predict the output.

**Answer:**

| Name  | Subject |
| ----- | ------- |
| Rahul | Python  |
| Rahul | SQL     |
| Priya | Python  |
| Priya | SQL     |
| Arjun | Python  |
| Arjun | SQL     |

### Q4

If there are 500 students and 8 subjects, how many rows?

**Answer:**

```text
500 × 8 = 4,000
```

---

# 🎤 Final line for your class

Ask:

> **“If I want matching data, which JOIN?”**

**INNER JOIN**

> **“If I want every student?”**

**LEFT JOIN**

> **“If I want every department?”**

**RIGHT JOIN**

> **“If I want everybody from both sides?”**

**FULL OUTER JOIN**

> **“If I want every possible combination?”**

# 💥 CROSS JOIN

And give them this memory trick:

> **“CROSS JOIN doesn't find relationships. It creates possibilities.”**

---

## 🔜 NEXT TOPIC: JOINING 3 OR MORE TABLES

This is where we move from simple JOINs to **real-world SQL queries**.

We'll build a **Student → Enrollment → Course → Department** chain and teach them how SQL can connect **3 tables, 4 tables, and more**, with each case progressively harder and every answer included.
