# 🔥 TOPIC 3: SELF JOIN — “The Employee–Manager Mystery”

Now we move to **SELF JOIN**.

Don't introduce the term immediately. Let students discover the problem first.

---

## 🎬 1. Start with the mystery

Tell the students:

> **“I have one table. I want to find every employee and their manager. But there is no separate Manager table. How can we do it?”**

Show them:

### `employees`

| emp_id | name  | manager_id |
| -----: | ----- | ---------: |
|      1 | Rahul |       NULL |
|      2 | Priya |          1 |
|      3 | Arjun |          1 |
|      4 | Sneha |          2 |
|      5 | Kiran |          2 |

Ask:

> **“Where is Priya's manager?”**

Look at:

```text
Priya → manager_id = 1
```

Then ask:

> **“Who is employee 1?”**

Rahul.

So:

```text
Priya
  |
  | manager_id = 1
  ↓
Rahul
```

🔥 **The employee and manager are stored in the SAME table.**

That is the problem.

---

# 🧠 2. Can we JOIN a table with itself?

Ask students:

> **“Can we join `employees` with `employees`?”**

Yes!

But there's a problem.

If we write:

```sql
employees
employees
```

SQL needs to know:

> Which copy represents the employee?

and

> Which copy represents the manager?

So we create **two aliases**.

```text
employees e
employees m
```

Think:

```text
e = employee
m = manager
```

---

# 💻 3. The SELF JOIN

```sql
SELECT
    e.name AS employee,
    m.name AS manager
FROM employees e
JOIN employees m
ON e.manager_id = m.emp_id;
```

### Output

| employee | manager |
| -------- | ------- |
| Priya    | Rahul   |
| Arjun    | Rahul   |
| Sneha    | Priya   |
| Kiran    | Priya   |

---

# 🔍 4. Understand the magic

This line:

```sql
FROM employees e
```

means:

> Treat one copy of `employees` as the employee.

This line:

```sql
JOIN employees m
```

means:

> Treat another copy of `employees` as the manager.

Then:

```sql
ON e.manager_id = m.emp_id
```

means:

> **Employee's manager ID must match another employee's employee ID.**

That's the whole trick.

---

# 🧠 Draw this for students

```text
             employees
          ┌──────────────┐
          │              │
       e = employee    m = manager
          │              │
          │ manager_id   │ emp_id
          └──────┬───────┘
                 │
                 =
```

Or use the actual data:

```text
Priya
manager_id = 1
     │
     ↓
emp_id = 1
Rahul
```

---

# ⚠️ 5. Why did Rahul disappear?

Ask:

> **“Where is Rahul?”**

He's not in the output.

Why?

Rahul has:

```text
manager_id = NULL
```

He doesn't have a manager.

Since we're using an ordinary `JOIN`/`INNER JOIN`, only matching rows appear.

So:

```text
Rahul → NULL → no match → disappears
```

---

# 🔥 CASE 1 — Find Employee + Manager

### Question

Using:

```text
employees
```

find:

> Employee name and manager name.

### 🧠 Give students 2 minutes.

### ✅ Answer

```sql
SELECT
    e.name AS employee,
    m.name AS manager
FROM employees e
INNER JOIN employees m
ON e.manager_id = m.emp_id;
```

### Output

| employee | manager |
| -------- | ------- |
| Priya    | Rahul   |
| Arjun    | Rahul   |
| Sneha    | Priya   |
| Kiran    | Priya   |

---

# 🎯 CASE 2 — Now the CEO Must Appear

Now change the requirement:

> **“The HR department wants EVERY employee, including the CEO who doesn't have a manager.”**

Students should realize:

**INNER JOIN won't work.**

We need:

```sql
LEFT JOIN
```

### Answer

```sql
SELECT
    e.name AS employee,
    m.name AS manager
FROM employees e
LEFT JOIN employees m
ON e.manager_id = m.emp_id;
```

### Output

| employee | manager |
| -------- | ------- |
| Rahul    | NULL    |
| Priya    | Rahul   |
| Arjun    | Rahul   |
| Sneha    | Priya   |
| Kiran    | Priya   |

🔥 Now Rahul stays.

Tell students:

> **“The LEFT side is the employee. Therefore every employee survives.”**

---

# 🕵️ CASE 3 — Find Only Employees Who Have Managers

Question:

> **“Show employees who actually have a manager.”**

### Answer

```sql
SELECT
    e.name AS employee,
    m.name AS manager
FROM employees e
INNER JOIN employees m
ON e.manager_id = m.emp_id;
```

The result:

| employee | manager |
| -------- | ------- |
| Priya    | Rahul   |
| Arjun    | Rahul   |
| Sneha    | Priya   |
| Kiran    | Priya   |

---

# 🔥 CASE 4 — Who Reports to Rahul?

Now make it more interesting.

Question:

> **“Find all employees who report directly to Rahul.”**

Rahul's `emp_id` is:

```text
1
```

### Answer

```sql
SELECT e.name
FROM employees e
JOIN employees m
ON e.manager_id = m.emp_id
WHERE m.name = 'Rahul';
```

### Output

| name  |
| ----- |
| Priya |
| Arjun |

---

# 🧠 Understand the query

First:

```sql
e.manager_id = m.emp_id
```

connects employee → manager.

Then:

```sql
WHERE m.name = 'Rahul'
```

says:

> Only keep the rows where the manager is Rahul.

---

# 💥 CASE 5 — Find Priya's Employees

Question:

> **“Who reports directly to Priya?”**

### Answer

```sql
SELECT e.name
FROM employees e
JOIN employees m
ON e.manager_id = m.emp_id
WHERE m.name = 'Priya';
```

### Output

| name  |
| ----- |
| Sneha |
| Kiran |

---

# 🧩 CASE 6 — Manager ID Instead of Manager Name

Question:

> Display:

```text
Employee
Manager ID
```

### Answer

```sql
SELECT
    e.name,
    e.manager_id
FROM employees e;
```

Output:

| name  | manager_id |
| ----- | ---------: |
| Rahul |       NULL |
| Priya |          1 |
| Arjun |          1 |
| Sneha |          2 |
| Kiran |          2 |

Now ask:

> **“Is `1` useful to a normal HR person?”**

Not really.

They want:

```text
Priya → Rahul
```

That's why we need the SELF JOIN.

---

# 🧠 CASE 7 — The Most Important Prediction

Give them:

### Employees

| ID | Name | Manager ID |
| -: | ---- | ---------: |
| 10 | A    |       NULL |
| 20 | B    |         10 |
| 30 | C    |         10 |
| 40 | D    |         20 |

Ask:

> **“If we INNER JOIN this table with itself, how many rows?”**

Let's manually solve:

```text
B → manager 10 → A ✅
C → manager 10 → A ✅
D → manager 20 → B ✅
A → manager NULL → ❌
```

### Answer:

**3 rows**

Output:

| employee | manager |
| -------- | ------- |
| B        | A       |
| C        | A       |
| D        | B       |

---

# 🚨 CASE 8 — Find Employees Without Managers

Now flip the problem.

> **“Find the employees who don't have a manager.”**

This time, we don't need a SELF JOIN at all.

```sql
SELECT name
FROM employees
WHERE manager_id IS NULL;
```

### Output

| name  |
| ----- |
| Rahul |

This is a good teaching moment:

> **“Don't use JOIN just because you learned JOIN. First understand the question.”**

That's an excellent SQL habit.

---

# 🏆 FINAL CHALLENGE

Give students this data:

### `employees`

| emp_id | name    | manager_id |
| -----: | ------- | ---------: |
|    101 | Alice   |       NULL |
|    102 | Bob     |        101 |
|    103 | Charlie |        101 |
|    104 | David   |        102 |
|    105 | Emma    |        102 |
|    106 | Frank   |        103 |

Ask them to solve these **one by one**.

---

### Challenge A

> Display every employee and their manager.

### Answer

```sql
SELECT
    e.name AS employee,
    m.name AS manager
FROM employees e
LEFT JOIN employees m
ON e.manager_id = m.emp_id;
```

### Output

| employee | manager |
| -------- | ------- |
| Alice    | NULL    |
| Bob      | Alice   |
| Charlie  | Alice   |
| David    | Bob     |
| Emma     | Bob     |
| Frank    | Charlie |

---

### Challenge B

> Who reports directly to Alice?

### Answer

```sql
SELECT e.name
FROM employees e
JOIN employees m
ON e.manager_id = m.emp_id
WHERE m.name = 'Alice';
```

### Output

```text
Bob
Charlie
```

---

### Challenge C

> Who reports directly to Bob?

### Answer

```sql
SELECT e.name
FROM employees e
JOIN employees m
ON e.manager_id = m.emp_id
WHERE m.name = 'Bob';
```

### Output

```text
David
Emma
```

---

### Challenge D

> Find the employee who has no manager.

### Answer

```sql
SELECT name
FROM employees
WHERE manager_id IS NULL;
```

### Output

```text
Alice
```

---

# 🧠 The one sentence they must remember

Write this at the end:

> **SELF JOIN = Joining a table with itself by giving its two copies different aliases.**

And the classic pattern:

```sql
SELECT e.name, m.name
FROM employees e
JOIN employees m
ON e.manager_id = m.emp_id;
```

Tell them:

> **“One physical table. Two logical roles.”**

That's the key idea.

---

## 🔜 Next topic: RIGHT JOIN

We'll use a **“Missing Department” investigation** to make them discover why `RIGHT JOIN` exists, and we'll compare **INNER vs LEFT vs RIGHT** using the **same data**, with prediction questions and answers.
