# 🔥 TOPIC 4: RIGHT JOIN — “The Department Audit”

For this topic, don't begin with the definition. First create a problem where **LEFT JOIN feels insufficient**.

---

# 🎬 1. Start with a real-world situation

Tell your students:

> **“The college administration doesn't want a student report today. They want a report of ALL departments — even departments that currently have ZERO students.”**

Show:

### `students`

| student_id | name   | dept_id |
| ---------: | ------ | ------: |
|        101 | Rahul  |      10 |
|        102 | Ananya |      20 |
|        103 | Kiran  |      10 |
|        104 | Sneha  |      20 |

### `departments`

| dept_id | dept_name  |
| ------: | ---------- |
|      10 | CSE        |
|      20 | ECE        |
|      30 | EEE        |
|      40 | Mechanical |

Ask:

> **“Which departments currently have students?”**

Students:

```text
CSE
ECE
```

Then ask:

> **“Which departments have ZERO students?”**

```text
EEE
Mechanical
```

Now the interesting question:

> **“Can INNER JOIN show us EEE and Mechanical?”**

❌ No.

Because there is no matching student.

---

# 🧠 2. Why LEFT JOIN doesn't solve this particular question

If we write:

```sql
SELECT s.name, d.dept_name
FROM students s
LEFT JOIN departments d
ON s.dept_id = d.dept_id;
```

What does LEFT JOIN guarantee?

> **Every student.**

But our requirement is:

> **Every department.**

So the table we want to preserve needs to be on the **RIGHT**.

---

# 💡 3. Introduce RIGHT JOIN

```sql
SELECT s.name, d.dept_name
FROM students s
RIGHT JOIN departments d
ON s.dept_id = d.dept_id;
```

Now:

```text
LEFT TABLE             RIGHT TABLE
students       RIGHT JOIN       departments
                                  ↑
                            KEEP ALL
```

### Output

| name   | dept_name  |
| ------ | ---------- |
| Rahul  | CSE        |
| Kiran  | CSE        |
| Ananya | ECE        |
| Sneha  | ECE        |
| NULL   | EEE        |
| NULL   | Mechanical |

🔥 Now every department appears.

---

# ⭐ The golden rule

Write this prominently:

```text
RIGHT JOIN
     ↓
KEEP EVERYTHING FROM THE RIGHT TABLE
+
MATCHING DATA FROM THE LEFT TABLE
```

Or tell them:

> **“RIGHT JOIN protects the RIGHT table.”**

---

# 🔍 4. Understand the query

```sql
SELECT s.name, d.dept_name
```

We want:

```text
Student Name
Department Name
```

---

```sql
FROM students s
```

`students` is the **LEFT table**.

---

```sql
RIGHT JOIN departments d
```

`departments` is the **RIGHT table**.

Therefore:

> Every department must appear.

---

```sql
ON s.dept_id = d.dept_id
```

This tells SQL how to match them.

---

# 🧠 5. Prediction Game

Give students:

### Students

| Name | Dept ID |
| ---- | ------: |
| A    |      10 |
| B    |      20 |
| C    |      10 |

### Departments

| Dept ID | Department |
| ------: | ---------- |
|      10 | CSE        |
|      20 | ECE        |
|      30 | EEE        |

Ask:

> **“RIGHT JOIN — how many rows?”**

Don't run it.

### Let's reason:

```text
CSE → A, C       = 2 rows
ECE → B          = 1 row
EEE → no student = 1 row
```

### Answer:

**4 rows**

### Output

| name | department |
| ---- | ---------- |
| A    | CSE        |
| C    | CSE        |
| B    | ECE        |
| NULL | EEE        |

---

# 🔥 6. Important concept: One-to-Many Matching

Students often think:

> One department = one student.

Break that assumption.

CSE has:

```text
A
C
```

So one department can match **multiple students**.

Therefore:

```text
CSE
 ↓
A
C
```

A JOIN doesn't simply "attach one row to another."

It produces rows based on **all matching combinations**.

---

# 🕵️ CASE 1 — Department Audit

### Data

**Employees**

| Employee | Dept ID |
| -------- | ------: |
| Rahul    |      10 |
| Priya    |      10 |
| Arjun    |      20 |

**Departments**

| Dept ID | Department  |
| ------- | ----------- |
| 10      | Development |
| 20      | Testing     |
| 30      | HR          |
| 40      | Finance     |

### Question

> Display **all departments** and their employees, including departments with no employees.

### 🧠 Think

The requirement says:

**ALL departments**

So departments must be preserved.

### ✅ Answer

```sql
SELECT d.dept_name, e.name
FROM employees e
RIGHT JOIN departments d
ON e.dept_id = d.dept_id;
```

### Output

| dept_name   | name  |
| ----------- | ----- |
| Development | Rahul |
| Development | Priya |
| Testing     | Arjun |
| HR          | NULL  |
| Finance     | NULL  |

---

# 🎯 CASE 2 — Find Departments With ZERO Employees

Now make it more interesting.

Question:

> **“Find departments that don't have any employees.”**

Using the previous data.

We know:

```text
HR
Finance
```

But how does SQL find them?

First perform:

```sql
RIGHT JOIN
```

Then look for:

```text
employee = NULL
```

### Answer

```sql
SELECT d.dept_name
FROM employees e
RIGHT JOIN departments d
ON e.dept_id = d.dept_id
WHERE e.name IS NULL;
```

### Output

| dept_name |
| --------- |
| HR        |
| Finance   |

🔥 This is a very useful pattern.

---

# 🧠 CASE 3 — Predict the Output

### Students

| Name | Dept |
| ---- | ---: |
| A    |   10 |
| B    |   10 |
| C    |   20 |

### Departments

| Dept | Name |
| ---- | ---- |
| 10   | CSE  |
| 20   | ECE  |
| 30   | EEE  |

Query:

```sql
SELECT d.dept_name, s.name
FROM students s
RIGHT JOIN departments d
ON s.dept_id = d.dept_id;
```

### Answer

| dept_name | name |
| --------- | ---- |
| CSE       | A    |
| CSE       | B    |
| ECE       | C    |
| EEE       | NULL |

Ask them:

> **“Why does CSE appear twice?”**

Because:

```text
CSE
 ↓
A
B
```

There are two matching students.

---

# 💥 CASE 4 — The Trick Question

Ask:

> **“Is RIGHT JOIN fundamentally different from LEFT JOIN?”**

Answer:

**No.**

They are mirror images.

For example:

```sql
FROM students s
RIGHT JOIN departments d
ON s.dept_id = d.dept_id;
```

can be rewritten as:

```sql
FROM departments d
LEFT JOIN students s
ON s.dept_id = d.dept_id;
```

Both preserve:

**departments**

### This is extremely important.

Tell students:

> **“RIGHT JOIN is basically LEFT JOIN after swapping the tables.”**

---

# 🔄 Compare Them

Use exactly the same data.

### INNER JOIN

```text
Only matches
```

```text
CSE → students
ECE → students
```

### LEFT JOIN

```text
Keep every student
```

```text
All students
+ department if available
```

### RIGHT JOIN

```text
Keep every department
```

```text
All departments
+ student if available
```

---

# 🧠 One table to make it crystal clear

| JOIN       | What is guaranteed?         |
| ---------- | --------------------------- |
| INNER JOIN | Only matching rows          |
| LEFT JOIN  | Everything from LEFT table  |
| RIGHT JOIN | Everything from RIGHT table |

Tell them:

> **“Don't memorize LEFT and RIGHT as SQL vocabulary. Look at the requirement: WHO must never disappear?”**

That's the real skill.

---

# 🏆 FINAL CHALLENGE

### `students`

| ID | Name    | Dept ID |
| -: | ------- | ------: |
|  1 | Ravi    |     101 |
|  2 | Priya   |     102 |
|  3 | Karthik |     101 |
|  4 | Sneha   |     105 |

### `departments`

|  ID | Department |
| --: | ---------- |
| 101 | CSE        |
| 102 | ECE        |
| 103 | EEE        |
| 104 | Mechanical |
| 105 | Civil      |

---

### Challenge A

> Display all departments and students.

### ✅ Answer

```sql
SELECT d.department, s.name
FROM students s
RIGHT JOIN departments d
ON s.dept_id = d.id;
```

### Output

| Department | Name    |
| ---------- | ------- |
| CSE        | Ravi    |
| CSE        | Karthik |
| ECE        | Priya   |
| EEE        | NULL    |
| Mechanical | NULL    |
| Civil      | Sneha   |

---

### Challenge B

> Find departments with no students.

### ✅ Answer

```sql
SELECT d.department
FROM students s
RIGHT JOIN departments d
ON s.dept_id = d.id
WHERE s.id IS NULL;
```

### Output

| Department |
| ---------- |
| EEE        |
| Mechanical |

---

### Challenge C

> Rewrite the RIGHT JOIN using LEFT JOIN.

### ✅ Answer

```sql
SELECT d.department, s.name
FROM departments d
LEFT JOIN students s
ON s.dept_id = d.id;
```

Same result.

---

# 🎤 End the class with this question

Ask everyone:

> **“I have two tables. I want to make sure that NO department disappears. Which JOIN should I consider?”**

Students:

# 👉 RIGHT JOIN

Then immediately ask:

> **“Could I also solve this using LEFT JOIN?”**

Students:

# 👉 YES!

Then:

> **“How?”**

> Put `departments` on the LEFT.

🔥 **That is the understanding you want—not memorizing syntax.**

---

## 🔜 NEXT: FULL OUTER JOIN

This one can be made into a **“Nobody Gets Left Behind” challenge**, where students must preserve **both unmatched students AND unmatched departments** and predict the complete result before running the query.
