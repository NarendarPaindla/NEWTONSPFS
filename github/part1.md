# Git & GitHub – Step-by-Step Complete Guide with Real-Time Examples

---

# 1. Why Version Control Exists?

## Problem Before Version Control

Imagine you are creating a training material.

Your folder looks like:

```text
Training.docx
Training_Final.docx
Training_Final_Updated.docx
Training_Final_Updated_Latest.docx
Training_Final_Updated_Latest_2.docx
```

Questions:

* Which file is the latest?
* Who changed what?
* Can we go back to yesterday's version?
* How can multiple people work together?

This is where **Version Control Systems (VCS)** help.

---

## What is Version Control?

Version Control is a system that:

* Tracks changes in files
* Stores history
* Allows rollback
* Supports team collaboration

Popular VCS:

* Git ✅ (Most popular)
* SVN
* Mercurial

---

## Real-Time Example

Think of **Google Docs Version History**.

You can:

* See who changed what
* Restore old versions
* Compare changes

Git does the same for code.

---

# 2. What is Git?

Git is a Distributed Version Control System created by

Linus Torvalds

Git tracks every change made to project files.

---

# 3. Git Workflow Overview

```text
Working Directory
       ↓
git add
       ↓
Staging Area
       ↓
git commit
       ↓
Repository (.git)
```

---

# 4. git init

## Purpose

Creates a new Git repository.

### Command

```bash
git init
```

### Example

```bash
mkdir StudentPortal
cd StudentPortal

git init
```

Output:

```text
Initialized empty Git repository
```

Git creates hidden folder:

```text
.git
```

This folder stores:

* commits
* branches
* history
* configuration

---

## Real-Time Example

You start a new Java project.

Before coding:

```bash
git init
```

Now Git starts tracking the project.

---

# 5. git add

## Purpose

Moves changes to the staging area.

---

### Example

Create file:

```text
index.html
```

Content:

```html
<h1>Welcome</h1>
```

Check status:

```bash
git status
```

Output:

```text
Untracked file:
index.html
```

Add file:

```bash
git add index.html
```

Or all files:

```bash
git add .
```

---

## Real-Time Example

Think of staging area like a shopping cart.

```text
Store = Working Directory

Cart = Staging Area

Purchase = Commit
```

You choose items first.

Then purchase.

Similarly:

```text
git add
```

selects changes.

---

# 6. Staging Area

## What is Staging Area?

Temporary place where Git keeps selected changes before commit.

---

### Flow

```text
File Modified
      ↓
git add
      ↓
Staging Area
      ↓
git commit
      ↓
Permanent History
```

---

## Why Staging Area?

Suppose:

```text
Login.java
```

You made:

1. Login bug fix
2. UI color change

You only want bug fix in current commit.

Stage only required changes.

This gives clean commit history.

---

# 7. git commit

## Purpose

Saves staged changes permanently.

### Command

```bash
git commit -m "Initial project setup"
```

---

### Example

```bash
git add .

git commit -m "Added home page"
```

Output:

```text
1 file changed
```

---

## Real-Time Example

A commit is like a save point in a video game.

```text
Level 1 Saved
Level 2 Saved
Level 3 Saved
```

If something breaks:

```text
Return to Level 2
```

Similarly Git allows rollback.

---

## Good Commit Messages

Bad:

```text
update
changes
done
```

Good:

```text
Added login page
Fixed payment calculation bug
Created user registration API
```

---

# 8. .gitignore

## Purpose

Tells Git what files should NOT be tracked.

---

### Example

Create:

```text
.gitignore
```

Content:

```text
node_modules/
*.log
*.class
.env
```

---

## Why?

Some files should not be stored:

### Generated Files

```text
bin/
target/
dist/
```

### Sensitive Data

```text
.env
application.properties
```

### Logs

```text
app.log
error.log
```

---

## Real-Time Example

Imagine exam rough sheets.

You don't submit them.

Similarly:

```text
Temporary files
Logs
Build files
```

should not go to Git.

---

# 9. Viewing History – git log

## Purpose

Shows commit history.

### Command

```bash
git log
```

Output:

```text
Commit A
Commit B
Commit C
```

---

### Compact Version

```bash
git log --oneline
```

Output:

```text
a12bc34 Added login page
d45ef67 Fixed bug
```

---

## Real-Time Example

Like browsing WhatsApp chat history.

You can see:

```text
Who
When
What
```

Git shows:

```text
Commit ID
Author
Date
Message
```

---

# 10. Comparing Changes – git diff

## Purpose

Shows differences between versions.

### Command

```bash
git diff
```

---

### Example

Before:

```java
int age = 18;
```

After:

```java
int age = 21;
```

Git shows:

```diff
- int age = 18;
+ int age = 21;
```

---

## Real-Time Example

Like comparing:

```text
Old Resume
New Resume
```

and highlighting changes.

---

# 11. Branches

## What is a Branch?

A separate line of development.

---

### Default Branch

```text
main
```

---

### Create Branch

```bash
git branch feature-login
```

---

### Switch Branch

```bash
git checkout feature-login
```

Modern command:

```bash
git switch feature-login
```

---

### Create + Switch

```bash
git checkout -b feature-login
```

or

```bash
git switch -c feature-login
```

---

## Real-Time Example

Main road:

```text
main
```

Side road:

```text
feature-login
```

You build feature separately without affecting main code.

---

# 12. Merge

## Purpose

Combine branch changes.

---

### Scenario

```text
main
  |
feature-login
```

After development:

```bash
git checkout main

git merge feature-login
```

---

### Result

```text
main
contains login feature
```

---

## Real-Time Example

Two trainers prepare different modules.

Trainer A:

```text
Java
```

Trainer B:

```text
Spring Boot
```

Merge combines both into one training material.

---

# 13. Merge Conflicts

## When Conflict Happens?

Same line edited in two branches.

---

### Main Branch

```java
String name = "User";
```

### Feature Branch

```java
String name = "Admin";
```

Git doesn't know which is correct.

---

### Conflict Output

```text
<<<<<<< HEAD
User
=======
Admin
>>>>>>> feature-login
```

---

## Resolve Conflict

Choose:

```java
String name = "Admin";
```

Then:

```bash
git add .
git commit -m "Resolved merge conflict"
```

---

## Real-Time Example

Two employees edit the same paragraph in a document.

Git asks:

```text
Which version should I keep?
```

Human decides.

---

# 14. GitHub Basics

## What is GitHub?

GitHub is a cloud platform that hosts Git repositories.

Think:

```text
Git = Technology

GitHub = Online Storage + Collaboration
```

GitHub is owned by GitHub.

---

# 15. Clone Repository

## Purpose

Download project from GitHub.

### Command

```bash
git clone https://github.com/company/project.git
```

---

### Example

```bash
git clone https://github.com/training/studentportal.git
```

Creates:

```text
studentportal/
```

with complete history.

---

## Real-Time Example

Downloading shared project from company server.

---

# 16. Remote Repository

## What is Remote?

Online copy of repository.

Check remote:

```bash
git remote -v
```

Output:

```text
origin
```

---

### Add Remote

```bash
git remote add origin URL
```

Example:

```bash
git remote add origin https://github.com/user/project.git
```

---

## Real-Time Example

Local Laptop:

```text
Local Repository
```

Cloud Backup:

```text
GitHub Repository
```

GitHub acts as remote storage.

---

# 17. Push

## Purpose

Upload local commits to GitHub.

### Command

```bash
git push origin main
```

---

### Flow

```text
Local Repo
      ↓
git push
      ↓
GitHub
```

---

## Real-Time Example

Saving project to company server.

---

# 18. Pull

## Purpose

Download latest changes from GitHub.

### Command

```bash
git pull origin main
```

---

### Flow

```text
GitHub
    ↓
git pull
    ↓
Local Machine
```

---

## Real-Time Example

Before starting office work every morning:

```bash
git pull
```

to get teammates' latest code.

---

# 19. Complete Team Workflow

Developer A:

```bash
git pull
git checkout -b feature-login
```

Develop code.

```bash
git add .
git commit -m "Added login feature"
git push origin feature-login
```

Developer B continues working on another feature.

Both work independently.

---

# 20. Pull Requests (PR)

## What is Pull Request?

A request to merge code into another branch.

Usually:

```text
feature-login
       ↓
main
```

---

## Why PR?

Before merging:

* Code review
* Discussion
* Testing
* Approval

---

### Workflow

```text
Create Branch
      ↓
Code Changes
      ↓
Push Branch
      ↓
Create Pull Request
      ↓
Review
      ↓
Approve
      ↓
Merge
```

---

## Real-Time Example

Employee submits report.

Manager reviews.

```text
Approved → Publish
Rejected → Fix Issues
```

PR works exactly like this.

---

# End-to-End Industry Example

### Feature: Student Login

```bash
git clone repo
git pull
git switch -c feature-login
```

Develop feature.

```bash
git add .
git commit -m "Added student login page"
```

Upload:

```bash
git push origin feature-login
```

Create Pull Request.

Reviewer checks:

```text
Code Quality
Security
Performance
Standards
```

After approval:

```bash
Merge PR
```

Finally:

```bash
git pull origin main
```

Everyone gets the latest code.

---

# Most Used Git Commands Cheat Sheet

```bash
git init

git status

git add .

git commit -m "message"

git log --oneline

git diff

git branch

git switch branch-name

git switch -c new-branch

git merge branch-name

git remote -v

git clone URL

git pull origin main

git push origin main
```

### Golden Rule

```text
Write Code
   ↓
git add
   ↓
git commit
   ↓
git push
   ↓
Pull Request
   ↓
Review
   ↓
Merge
```

This is the workflow followed in most software companies using Git and GitHub.
