# Git & GitHub – Part 2 

Now that you know `init`, `add`, `commit`, branches, merge, and GitHub basics, let's move to concepts commonly used in companies.

---

# 21. Git Status

## Purpose

Shows the current state of your repository.

### Command

```bash
git status
```

### Example

```bash
git status
```

Output:

```text
On branch main

modified: Login.java

untracked: User.java
```

### Meaning

| Status    | Meaning                     |
| --------- | --------------------------- |
| Untracked | Git doesn't know about file |
| Modified  | File changed                |
| Staged    | Ready for commit            |
| Committed | Saved in Git history        |

---

## Real-Time Example

Like checking order status:

```text
Added to cart
Ready for checkout
Purchased
Delivered
```

Git status tells where files are.

---

# 22. Understanding Git States

Every file goes through:

```text
Untracked
    ↓
git add
    ↓
Staged
    ↓
git commit
    ↓
Committed
```

Example:

```text
Student.java
```

Create file:

```text
Untracked
```

Run:

```bash
git add Student.java
```

Now:

```text
Staged
```

Run:

```bash
git commit -m "Added Student class"
```

Now:

```text
Committed
```

---

# 23. Viewing Specific Commit Details

## Show Commit Information

```bash
git show commit-id
```

Example:

```bash
git show a12bc34
```

Output includes:

* Author
* Date
* Message
* Code changes

---

## Real-Time Example

Like opening a specific bank transaction to see details.

---

# 24. Undo Changes Before Staging

Suppose:

```java
System.out.println("Hello");
```

You accidentally change it:

```java
System.out.println("Wrong Code");
```

Want to discard changes?

```bash
git restore FileName.java
```

Example:

```bash
git restore Login.java
```

---

## Real-Time Example

Like pressing Undo in MS Word.

---

# 25. Unstage Files

Suppose:

```bash
git add .
```

You accidentally staged a file.

Remove from staging:

```bash
git restore --staged Login.java
```

---

### Before

```text
Staged
```

### After

```text
Modified
```

File remains, only staging removed.

---

# 26. Amend Last Commit

Forgot a file?

Instead of creating another commit:

```bash
git add config.properties

git commit --amend
```

or

```bash
git commit --amend -m "Updated login feature"
```

---

## Real-Time Example

Submitted assignment.

Teacher allows one correction before grading.

---

# 27. Git Tags

## Purpose

Mark important releases.

Example:

```text
Version 1.0
Version 2.0
Version 3.0
```

---

### Create Tag

```bash
git tag v1.0
```

View tags:

```bash
git tag
```

---

### Push Tags

```bash
git push origin v1.0
```

---

## Real-Time Example

Software Releases:

```text
WhatsApp v2.0
WhatsApp v2.1
WhatsApp v3.0
```

Tags identify release points.

---

# 28. Git Ignore Best Practices

Typical Java Project:

```text
.gitignore
```

```gitignore
*.class
*.log
target/
.idea/
bin/
```

---

### Spring Boot Project

```gitignore
target/
*.log
application-local.properties
```

---

### Node.js Project

```gitignore
node_modules/
.env
dist/
```

---

### Python Project

```gitignore
__pycache__/
venv/
*.pyc
```

---

# 29. What is HEAD?

HEAD points to current branch and current commit.

Example:

```text
Commit A
Commit B
Commit C ← HEAD
```

You are currently on Commit C.

---

### Check HEAD

```bash
git log --oneline
```

Latest commit is where HEAD points.

---

## Real-Time Example

Think of a bookmark in a book.

HEAD tells Git:

```text
You are here
```

---

# 30. Merge Types

## Fast Forward Merge

```text
A → B → C
         \
          D
```

Merge:

```bash
git merge feature
```

Result:

```text
A → B → C → D
```

No merge commit created.

---

## Three-Way Merge

```text
       D
      /
A → B
      \
       E
```

After merge:

```text
       D
      /
A → B ----- M
      \
       E
```

M = Merge Commit

---

# 31. Rebase (Important Interview Topic)

## What is Rebase?

Moves branch commits onto another branch.

---

### Before

```text
main:
A → B

feature:
A → B → C → D
```

---

### Command

```bash
git rebase main
```

---

### Result

```text
A → B → C' → D'
```

Cleaner history.

---

## Difference

### Merge

```text
History preserved
Extra merge commits
```

### Rebase

```text
Linear history
Cleaner
```

---

## Company Practice

Usually:

```bash
git pull --rebase
```

used to keep history clean.

---

# 32. Cherry Pick

## Purpose

Copy one specific commit.

---

### Example

Need only one bug fix commit.

```bash
git cherry-pick commit-id
```

Example:

```bash
git cherry-pick a12bc34
```

---

## Real-Time Example

Instead of copying an entire notebook, copy only one page.

---

# 33. Stash

## Problem

You're working.

Suddenly manager says:

```text
Fix production bug now.
```

Your code is incomplete.

---

### Save Work Temporarily

```bash
git stash
```

Git stores changes safely.

---

### View Stashes

```bash
git stash list
```

---

### Restore

```bash
git stash pop
```

---

## Real-Time Example

Like putting unfinished work in a drawer temporarily.

---

# 34. Fork vs Clone

## Clone

```text
Your repository copy
```

```bash
git clone URL
```

Used daily.

---

## Fork

GitHub creates:

```text
Original Repo
       ↓
Your Own Repo
```

Used in Open Source.

---

### Example

You want to contribute to

Apache Software Foundation projects.

Steps:

```text
Fork
Clone
Modify
Push
Pull Request
```

---

# 35. Pull Request Lifecycle

## Step 1

Create Branch

```bash
git switch -c payment-feature
```

---

## Step 2

Commit

```bash
git add .
git commit -m "Added payment gateway"
```

---

## Step 3

Push

```bash
git push origin payment-feature
```

---

## Step 4

Create PR in GitHub

```text
payment-feature → main
```

---

## Step 5

Code Review

Reviewer checks:

* Naming conventions
* Security
* Logic
* Performance

---

## Step 6

Fix Comments

```bash
git commit
git push
```

PR automatically updates.

---

## Step 7

Approve & Merge

Feature becomes part of main branch.

---

# 36. Common Interview Questions

## Q1: Difference Between Git and GitHub?

### Git

```text
Version Control Tool
Works locally
```

### GitHub

```text
Cloud Hosting Platform
Stores Git repositories
```

---

## Q2: Difference Between Add and Commit?

### git add

```text
Moves changes to staging
```

### git commit

```text
Saves changes permanently
```

---

## Q3: Difference Between Merge and Rebase?

### Merge

```text
Preserves history
Creates merge commit
```

### Rebase

```text
Linear history
No merge commit
```

---

## Q4: What is Staging Area?

Temporary area between:

```text
Working Directory
and
Repository
```

---

## Q5: What causes Merge Conflict?

When same lines are modified in multiple branches.

---

# 37. Complete Real-Time Company Workflow

```text
Developer Pulls Latest Code
            ↓
Create Feature Branch
            ↓
Write Code
            ↓
git add
            ↓
git commit
            ↓
git push
            ↓
Create Pull Request
            ↓
Code Review
            ↓
Fix Comments
            ↓
Approval
            ↓
Merge
            ↓
Deploy
```

---

# Most Important Commands for Freshers

```bash
git init

git status

git add .

git commit -m "message"

git log --oneline

git diff

git branch

git switch -c feature-name

git merge feature-name

git stash

git stash pop

git clone URL

git pull origin main

git push origin main

git tag v1.0
```

### Next Topics (Part 3 – Advanced Git & GitHub)

* Git Internals (.git folder)
* Detached HEAD
* Reset (soft, mixed, hard)
* Revert
* Squash Commits
* Interactive Rebase
* Git Hooks
* Git Flow Strategy
* Trunk-Based Development
* CI/CD with GitHub Actions
* Branch Protection Rules
* Real Industry Git Workflow for Teams of 50+ Developers
