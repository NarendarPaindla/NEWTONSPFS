Absolutely. From now on, for **every practice case**, I'll give the **question → time to think → answer → SQL → output → explanation** so you can teach directly without needing to prepare answers separately.

First, here are the answers to the previous cases.

---

# ✅ INNER JOIN — Answers to Previous Cases

## Case 1 — College Clubs

### Data

**Students**

| ID | Name  | Club ID |
| -: | ----- | ------: |
|  1 | Arjun |     101 |
|  2 | Priya |     102 |
|  3 | Ravi  |     101 |
|  4 | Meena |     105 |

**Clubs**

| Club ID | Club Name   |
| ------: | ----------- |
|     101 | Coding Club |
|     102 | Robotics    |
|     103 | Music       |
|     104 | Photography |

### Question

Find every student's name and their club name using `INNER JOIN`.

### Answer

```sql
SELECT s.name, c.club_name
FROM students s
INNER JOIN clubs c
ON s.club_id = c.club_id;
```

### Output

| name  | club_name   |
| ----- | ----------- |
| Arjun | Coding Club |
| Priya | Robotics    |
| Ravi  | Coding Club |

### Why is Meena missing?

```text
Meena → club_id = 105
             ↓
       No 105 in clubs
             ↓
           ❌
```

Because `INNER JOIN` keeps **only matching rows**.

---

# Case 2 — Products & Categories

### Question

Find product names and category names.

### Answer

```sql
SELECT p.product_name, c.category_name
FROM products p
INNER JOIN categories c
ON p.category_id = c.category_id;
```

### Output

| product_name | category_name |
| ------------ | ------------- |
| Laptop       | Electronics   |
| Mouse        | Accessories   |
| Keyboard     | Accessories   |

### Why is Camera missing?

```text
Camera → category_id = 30
              ↓
Categories has 10, 20, 40
              ↓
          30 doesn't exist
              ↓
              ❌
```

---

# Case 3 — Employees & Departments

### Question

How many rows will the INNER JOIN produce?

Employees:

```text
Ravi     → 10
Priya    → 20
Karthik  → 30
```

Departments:

```text
10 → Development
20 → Testing
40 → HR
```

### Answer

**2 rows**

Because:

```text
10 → MATCH ✅
20 → MATCH ✅
30 → NO MATCH ❌
```

### Output

| name  | dept_name   |
| ----- | ----------- |
| Ravi  | Development |
| Priya | Testing     |

Karthik disappears.

---

# Case 4 — Students & Courses

### Question

How many students appear?

Students:

```text
A → 101
B → 102
C → 103
D → 101
E → 105
```

Courses:

```text
101 → Python
102 → Java
104 → SQL
```

### Answer

**3 students**

```text
A → 101 → MATCH ✅
B → 102 → MATCH ✅
C → 103 → ❌
D → 101 → MATCH ✅
E → 105 → ❌
```

### SQL

```sql
SELECT s.name, c.course_name
FROM students s
INNER JOIN courses c
ON s.course_id = c.course_id;
```

### Output

| name | course_name |
| ---- | ----------- |
| A    | Python      |
| B    | Java        |
| D    | Python      |

---

# 🔥 NEXT TOPIC: LEFT JOIN

Now we move **only one step forward**.

# Topic 2 — LEFT JOIN

## 🎬 Start with a problem

Tell your students:

> **"Yesterday we made a mistake. We used INNER JOIN and two students disappeared. Today the Dean wants EVERY student, even if their department information is missing."**

Show:

### Students

|  ID | Name   | Dept ID |
| --: | ------ | ------: |
| 101 | Rahul  |      10 |
| 102 | Ananya |      20 |
| 103 | Kiran  |      30 |
| 104 | Sneha  |      50 |

### Departments

| Dept ID | Department |
| ------: | ---------- |
|      10 | CSE        |
|      20 | ECE        |
|      30 | EEE        |

Ask:

> **"If we use INNER JOIN, what happens to Sneha?"**

Students:

> She disappears.

Then say:

> **"But the Dean says: I don't care whether Sneha has a valid department. I want Sneha in my report."**

Now we have a new requirement.

---

# 🧠 The requirement

We want:

```text
EVERY student
     +
department information if available
```

This is exactly where **LEFT JOIN** comes in.

---

# 💻 SQL

```sql
SELECT s.name, d.dept_name
FROM students s
LEFT JOIN departments d
ON s.dept_id = d.dept_id;
```

### Output

| name   | dept_name |
| ------ | --------- |
| Rahul  | CSE       |
| Ananya | ECE       |
| Kiran  | EEE       |
| Sneha  | NULL      |

---

# ⭐ The most important rule

Write this on the screen:

```text
LEFT JOIN
    ↓
KEEP EVERYTHING FROM LEFT TABLE
    +
MATCHING DATA FROM RIGHT TABLE
```

In our query:

```sql
FROM students s
LEFT JOIN departments d
```

`students` is the **LEFT table**.

Therefore:

> **Every student must remain in the result.**

If a department is found → show it.

If a department isn't found → show `NULL`.

---

# 🧩 Case 1 — Predict Before Running

### Students

| Name | Course ID |
| ---- | --------: |
| A    |       101 |
| B    |       102 |
| C    |       105 |

### Courses

| Course ID | Course |
| --------: | ------ |
|       101 | Python |
|       102 | Java   |
|       103 | SQL    |

### Query

```sql
SELECT s.name, c.course_name
FROM students s
LEFT JOIN courses c
ON s.course_id = c.course_id;
```

### 🛑 Stop and ask students:

**How many rows?**

### ✅ Answer: 3

Why?

```text
A → 101 → Python ✅
B → 102 → Java   ✅
C → 105 → NULL   ⚠️
```

### Output

| name | course_name |
| ---- | ----------- |
| A    | Python      |
| B    | Java        |
| C    | NULL        |

---

# 🕵️ Case 2 — Find Students Without Courses

Now make the problem interesting.

Ask:

> **"Can we find students who haven't been assigned to a valid course?"**

Students:

| Name | Course ID |
| ---- | --------: |
| A    |       101 |
| B    |       102 |
| C    |       105 |
| D    |       101 |

Courses:

| Course ID | Course |
| --------: | ------ |
|       101 | Python |
|       102 | Java   |

First perform the LEFT JOIN.

Then:

```sql
SELECT s.name
FROM students s
LEFT JOIN courses c
ON s.course_id = c.course_id
WHERE c.course_id IS NULL;
```

### Answer

| name |
| ---- |
| C    |

### Why?

```text
C → 105
     ↓
No matching course
     ↓
c.course_id = NULL
```

This is a **very important real-world use of LEFT JOIN**.

---

# 🔥 Case 3 — Customers Who Never Ordered

Now give them a real-world problem.

### Customers

| ID | Name  |
| -: | ----- |
|  1 | Rahul |
|  2 | Priya |
|  3 | Arjun |
|  4 | Sneha |

### Orders

| Order ID | Customer ID |
| -------: | ----------: |
|      501 |           1 |
|      502 |           1 |
|      503 |           3 |

Ask:

> **"Find customers who have NEVER placed an order."**

### Think

We want:

```text
Every customer
      +
order if available
      ↓
find NULL orders
```

### Answer

```sql
SELECT c.name
FROM customers c
LEFT JOIN orders o
ON c.id = o.customer_id
WHERE o.order_id IS NULL;
```

### Output

| name  |
| ----- |
| Priya |
| Sneha |

### Why?

```text
Rahul → order exists ✅
Priya → no order ❌
Arjun → order exists ✅
Sneha → no order ❌
```

This is one of the most useful patterns to teach:

```sql
LEFT JOIN
...
WHERE right_table.id IS NULL
```

means:

> **"Find records on the left that have no matching record on the right."**

---

# 🧠 Case 4 — Important Trap

Ask:

> **"If I change LEFT JOIN to INNER JOIN in the previous query, can I still find customers who never ordered?"**

### Answer:

**No.**

Why?

INNER JOIN removes Priya and Sneha **before** the `WHERE` condition gets a chance to find them.

So:

```text
INNER JOIN
   ↓
No-match customers disappear
   ↓
Can't find them ❌
```

Whereas:

```text
LEFT JOIN
   ↓
Keep everyone
   ↓
No match becomes NULL
   ↓
WHERE ... IS NULL
   ↓
Find them ✅
```

This is a concept students are likely to remember.

---

# 🎯 Case 5 — Challenge

### Employees

| ID | Name  | Manager ID |
| -: | ----- | ---------: |
|  1 | Rahul |       NULL |
|  2 | Priya |          1 |
|  3 | Arjun |          1 |
|  4 | Sneha |          2 |

Ask:

> **"Display each employee and their manager's name."**

This is interesting because **the same table needs to be joined with itself**.

Don't solve it yet in class.

Tell them:

> **"Next case is going to break one of your assumptions about JOINs."**

Because that leads naturally to the next topic:

# 🔜 NEXT TOPIC: SELF JOIN

Students will discover that:

> **A table can JOIN with itself.**

And we'll solve it through the **Employee–Manager mystery**, followed by multiple practice cases with answers and output.
