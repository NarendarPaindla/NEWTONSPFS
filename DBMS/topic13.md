# 🔥 TOPIC 12: `IN` vs `NOT IN` vs `ANY` vs `ALL`

## 🎯 “Student vs Student Group” Challenge

This is where students usually start getting confused, so **don't teach these as four definitions**.

Teach them as four different questions.

---

# 🎬 THE STORY

Tell your students:

> “I will give you one student and a group of students. Your job is to compare them.”

Suppose:

### CSE Students

| Name  | Marks |
| ----- | ----: |
| Rahul |    85 |
| Arjun |    95 |
| Kiran |    78 |

We have another student:

| Name  | Marks |
| ----- | ----: |
| Priya |    90 |

Now ask different questions.

---

# 🧠 QUESTION 1: “Is this value present in the list?”

That's:

# `IN`

Suppose:

```text
CSE department IDs:
10
20
30
```

Question:

> Is department `20` one of these?

```text
20 IN (10,20,30)
```

✅ TRUE

But:

```text
50 IN (10,20,30)
```

❌ FALSE

---

# 🔥 CASE 1 — `IN` With a Subquery

Suppose:

### departments

| dept_id | dept_name |
| ------: | --------- |
|      10 | CSE       |
|      20 | ECE       |
|      30 | EEE       |

Question:

> Find students who belong to CSE or ECE.

First:

```sql
SELECT dept_id
FROM departments
WHERE dept_name IN ('CSE', 'ECE');
```

Result:

```text
10
20
```

Now use that result:

```sql
SELECT name, dept_id
FROM students
WHERE dept_id IN (
    SELECT dept_id
    FROM departments
    WHERE dept_name IN ('CSE', 'ECE')
);
```

### Meaning

```text
Outer query
   ↓
Student department ID
   ↓
Is it IN
   ↓
(10, 20)?
```

---

# 🎯 CASE 2 — Find Students Taking Python or Java

### courses

| course_id | course_name |
| --------: | ----------- |
|       501 | Python      |
|       502 | DBMS        |
|       503 | Java        |

Question:

> Find students enrolled in Python or Java.

### Answer

```sql
SELECT name
FROM students
WHERE student_id IN (
    SELECT student_id
    FROM enrollments
    WHERE course_id IN (
        SELECT course_id
        FROM courses
        WHERE course_name IN ('Python', 'Java')
    )
);
```

🔥 Here we have:

```text
Students
   ↓
Enrollments
   ↓
Courses
```

and `IN` at two levels.

---

# 🧠 `IN` MEMORY TRICK

Tell students:

> **`IN` = “Is it present in this group?”**

Example:

```sql
WHERE department IN ('CSE', 'ECE')
```

Think:

```text
CSE? → YES
ECE? → YES
EEE? → NO
```

---

# 🚫 QUESTION 2: “Is this value NOT present?”

That's:

# `NOT IN`

---

# 🎯 CASE 3 — Students NOT in CSE

Question:

> Find students who are not from CSE.

### Answer

```sql
SELECT name
FROM students
WHERE dept_id NOT IN (
    SELECT dept_id
    FROM departments
    WHERE dept_name = 'CSE'
);
```

If CSE ID is:

```text
10
```

then SQL effectively does:

```text
dept_id NOT IN (10)
```

So:

```text
10 → ❌
20 → ✅
30 → ✅
```

---

# ⚠️ IMPORTANT `NOT IN` WARNING

This is a good moment to teach a real-world SQL issue.

Suppose the subquery returns:

```text
10
20
NULL
```

Then `NOT IN` can produce unexpected results because of SQL's three-valued logic.

Tell beginners:

> **“If the subquery can return NULL, be careful with `NOT IN`. In production SQL, `NOT EXISTS` is often safer.”**

Don't go deep into `NULL` logic yet.

Just plant the idea.

---

# 🔥 Now We Enter `ANY`

This is where students usually struggle.

Don't start with syntax.

Start with English.

Suppose:

```text
CSE marks:
85
95
78
```

Priya scored:

```text
90
```

Ask:

> **“Is Priya's mark greater than at least ONE CSE student's mark?”**

Compare:

```text
90 > 85 ✅
90 > 95 ❌
90 > 78 ✅
```

At least one is true.

Therefore:

```text
90 > ANY (85,95,78)
```

✅ TRUE

---

# 🧠 `ANY`

> **ANY = At least one must satisfy the condition.**

Think:

```text
ANY = ONE OR MORE
```

---

# 🎯 CASE 4 — Find Students Scoring More Than ANY ECE Student

Suppose ECE marks are:

```text
72
80
```

Question:

> Find students whose marks are greater than at least one ECE student's mark.

### Answer

```sql
SELECT name, marks
FROM students
WHERE marks > ANY (
    SELECT marks
    FROM students
    WHERE dept_id = 20
);
```

Let's understand.

If ECE marks are:

```text
72
80
```

Then:

```text
marks > ANY (72,80)
```

means:

```text
marks > 72
OR
marks > 80
```

Effectively, for `>` comparison, the threshold is the **smallest** value.

So a student scoring `75` qualifies:

```text
75 > 72 ✅
75 > 80 ❌

At least one = TRUE
```

---

# 🔥 VERY IMPORTANT

For:

```sql
marks > ANY (...)
```

think:

> **“Greater than at least one.”**

For positive values, that effectively means:

> **Greater than the minimum value in the group.**

---

# 🎯 CASE 5 — `ANY` Prediction Game

Give students:

```text
Group A:
50
70
90
```

Student X = `75`

Ask:

```text
75 > ANY (50,70,90)
```

Check:

```text
75 > 50 → ✅
75 > 70 → ✅
75 > 90 → ❌
```

At least one is true.

### Answer:

✅ TRUE

---

# 🧩 CASE 6 — Another Prediction

Group:

```text
50
70
90
```

Student X:

```text
40
```

Question:

```text
40 > ANY (50,70,90)
```

Check:

```text
40 > 50 ❌
40 > 70 ❌
40 > 90 ❌
```

### Answer:

❌ FALSE

---

# 🚀 NOW `ALL`

Imagine the same marks:

```text
50
70
90
```

Student X:

```text
95
```

Question:

> Is X greater than **every** student in the group?

```text
95 > 50 ✅
95 > 70 ✅
95 > 90 ✅
```

Therefore:

```text
95 > ALL (50,70,90)
```

✅ TRUE

---

# 🧠 `ALL`

> **ALL = Every value must satisfy the condition.**

Memory trick:

```text
ANY → At least ONE
ALL → EVERY ONE
```

---

# 🔥 CASE 7 — `ALL` Example

ECE marks:

```text
72
80
```

Question:

> Find students scoring higher than every ECE student.

### Answer

```sql
SELECT name, marks
FROM students
WHERE marks > ALL (
    SELECT marks
    FROM students
    WHERE dept_id = 20
);
```

For a student scoring `85`:

```text
85 > 72 ✅
85 > 80 ✅
```

So:

✅ qualifies.

---

# 🧠 The Secret Behind `ANY` and `ALL`

For:

```sql
marks > ANY (...)
```

Think:

> **Greater than the minimum.**

For:

```sql
marks > ALL (...)
```

Think:

> **Greater than the maximum.**

So if:

```text
Group:
60
70
80
```

Then:

```text
X > ANY
```

roughly means:

```text
X > 60
```

while:

```text
X > ALL
```

means:

```text
X > 80
```

This is a **great conceptual shortcut**.

---

# 🔥 CASE 8 — `ANY` vs `ALL`

Give students:

```text
CSE marks:
60
75
90
```

Student:

```text
80
```

Ask:

### Q1

```text
80 > ANY (60,75,90)
```

Check:

```text
80 > 60 ✅
80 > 75 ✅
80 > 90 ❌
```

### Answer:

✅ TRUE

---

### Q2

```text
80 > ALL (60,75,90)
```

Check:

```text
80 > 60 ✅
80 > 75 ✅
80 > 90 ❌
```

### Answer:

❌ FALSE

This is the perfect example to make them **feel the difference**.

---

# 🎯 CASE 9 — `ANY` With `<`

Now change the operator.

Group:

```text
60
75
90
```

Student:

```text
70
```

Question:

```text
70 < ANY (60,75,90)
```

Check:

```text
70 < 60 ❌
70 < 75 ✅
70 < 90 ✅
```

At least one is true.

### Answer:

✅ TRUE

For `< ANY`, think:

> **Less than at least one value.**

Effectively:

> Less than the **maximum** value.

---

# 🧠 CASE 10 — `ALL` With `<`

```text
70 < ALL (60,75,90)
```

Check:

```text
70 < 60 ❌
70 < 75 ✅
70 < 90 ✅
```

One failed.

### Answer:

❌ FALSE

For:

```text
X < ALL (...)
```

think:

> X must be less than the **smallest** value.

---

# 📊 THE CHEAT SHEET

| Condition | Meaning                    |
| --------- | -------------------------- |
| `= ANY`   | Equal to at least one      |
| `> ANY`   | Greater than at least one  |
| `< ANY`   | Less than at least one     |
| `> ALL`   | Greater than every value   |
| `< ALL`   | Less than every value      |
| `IN`      | Matches one of the values  |
| `NOT IN`  | Matches none of the values |

---

# 🚨 IMPORTANT CONNECTION: `IN` vs `= ANY`

These are closely related:

```sql
WHERE dept_id IN (
    SELECT dept_id
    FROM departments
)
```

and:

```sql
WHERE dept_id = ANY (
    SELECT dept_id
    FROM departments
)
```

Both essentially mean:

> Match at least one value from the returned set.

For beginner teaching, tell them:

> **Use `IN` when you're simply checking membership. Use `ANY` when you're doing a comparison such as `>`, `<`, `>=`, etc.**

---

# 💥 REAL-WORLD CASE — Employee Salaries

### Employees

| Name  | Department | Salary |
| ----- | ---------- | -----: |
| Rahul | IT         |  60000 |
| Priya | IT         |  90000 |
| Arjun | HR         |  50000 |
| Sneha | HR         |  70000 |
| Kiran | IT         |  80000 |

Question:

> **Find employees earning more than every HR employee.**

HR salaries:

```text
50000
70000
```

We need:

```text
salary > ALL (50000,70000)
```

### Answer

```sql
SELECT name, salary
FROM employees
WHERE salary > ALL (
    SELECT salary
    FROM employees
    WHERE department = 'HR'
);
```

### Output

| Name  | Salary |
| ----- | -----: |
| Priya |  90000 |
| Kiran |  80000 |

Because both are:

```text
> 70000
```

---

# 🔥 CASE 11 — Earn More Than ANY HR Employee

Question:

> Find employees earning more than at least one HR employee.

### Answer

```sql
SELECT name, salary
FROM employees
WHERE salary > ANY (
    SELECT salary
    FROM employees
    WHERE department = 'HR'
);
```

HR:

```text
50000
70000
```

Therefore effectively:

```text
salary > 50000
```

### Output

| Name  | Salary |
| ----- | -----: |
| Rahul |  60000 |
| Priya |  90000 |
| Sneha |  70000 |
| Kiran |  80000 |

---

# 🤯 CASE 12 — Compare the Two

### Question A

```sql
salary > ANY (
    SELECT salary
    FROM employees
    WHERE department = 'HR'
)
```

Meaning:

> Salary is greater than **at least one** HR salary.

---

### Question B

```sql
salary > ALL (
    SELECT salary
    FROM employees
    WHERE department = 'HR'
)
```

Meaning:

> Salary is greater than **every** HR salary.

---

# 🏆 FINAL DETECTIVE CHALLENGE

Give students this dataset:

### Employees

| Name | Dept  | Salary |
| ---- | ----- | -----: |
| A    | IT    |  60000 |
| B    | IT    |  90000 |
| C    | HR    |  50000 |
| D    | HR    |  70000 |
| E    | Sales |  80000 |
| F    | Sales |  45000 |

Ask them **without running SQL**:

### Q1

Who earns more than **ANY HR employee**?

HR:

```text
50000
70000
```

Threshold for `> ANY`:

```text
> 50000
```

Answer:

```text
A → 60000 ✅
B → 90000 ✅
D → 70000 ✅
E → 80000 ✅
```

---

### Q2

Who earns more than **ALL HR employees**?

Threshold:

```text
> 70000
```

Answer:

```text
B → 90000 ✅
E → 80000 ✅
```

---

# 💡 Now Give Them the SQL

### Q1 Answer

```sql
SELECT name, salary
FROM employees
WHERE salary > ANY (
    SELECT salary
    FROM employees
    WHERE department = 'HR'
);
```

### Q2 Answer

```sql
SELECT name, salary
FROM employees
WHERE salary > ALL (
    SELECT salary
    FROM employees
    WHERE department = 'HR'
);
```

---

# 🧠 FINAL BOARD SUMMARY

Draw this on the screen:

```text
                 SUBQUERY
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
       IN         ANY          ALL
        │           │           │
   "Is it in?"  "At least    "Every
                 one?"        one?"
```

Then:

```text
IN
 ↓
Membership

ANY
 ↓
At least one condition is TRUE

ALL
 ↓
Every condition must be TRUE
```

### The most important sentence:

> 🔥 **ANY asks “Does at least one satisfy this?”**
> 🔥 **ALL asks “Do all of them satisfy this?”**

---

## 🎯 5-Minute Exit Test

Give them these **without answers first**:

### 1.

```sql
marks > ANY (70, 80, 90)
```

For `75` → **TRUE**

### 2.

```sql
marks > ALL (70, 80, 90)
```

For `75` → **FALSE**

### 3.

```sql
dept_id IN (10, 20, 30)
```

For `20` → **TRUE**

### 4.

```sql
dept_id NOT IN (10, 20, 30)
```

For `40` → **TRUE**

### 5.

HR salaries are `50K, 70K`.

Who earns more than **ALL** HR employees?

👉 Anyone earning **more than 70K**.

---

## 🔜 NEXT TOPIC: CORRELATED SUBQUERIES

This is the **big jump**.

We'll create the problem:

> **“Find employees who earn more than the average salary of THEIR OWN department.”**

This is different from the previous query because the inner query will **change for every outer row**.

We'll visualize it as:

```text
Rahul → calculate IT average → compare
Priya → calculate IT average → compare
Arjun → calculate HR average → compare
Sneha → calculate HR average → compare
```

That makes **correlated subqueries** much easier to understand.
