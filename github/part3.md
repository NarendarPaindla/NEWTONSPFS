# Git & GitHub – Part 3 (Advanced Git & Industry Practices)

These topics are frequently asked in interviews and used in real projects.

---

# 38. Git Internals – What's Inside `.git` Folder?

When you run:

```bash
git init
```

Git creates:

```text
.git/
├── objects
├── refs
├── HEAD
├── config
├── logs
```

### Important Files

#### HEAD

Stores current branch reference.

```text
ref: refs/heads/main
```

#### config

Repository settings.

#### objects

Stores commits, files, and Git data.

#### refs

Stores branch and tag references.

---

## Real-Time Example

Think of `.git` as a bank database.

Customers see transactions, but the actual records are stored internally.

Similarly:

```text
Project Files → Visible

.git → Internal Database
```

---

# 39. What is Detached HEAD?

Normally:

```text
HEAD → main → Commit C
```

Suppose you checkout an old commit:

```bash
git checkout a12bc34
```

Now:

```text
HEAD → Commit A
```

No branch attached.

This is called:

```text
Detached HEAD
```

---

## Why Use It?

To inspect old versions.

Example:

```bash
git checkout a12bc34
```

Check old code.

Return:

```bash
git switch main
```

---

## Real-Time Example

Reading an old edition of a textbook without changing the latest edition.

---

# 40. Git Reset

One of the most important Git concepts.

Used to move HEAD backward.

---

## Reset Types

### Soft Reset

```bash
git reset --soft HEAD~1
```

Removes commit.

Keeps changes staged.

---

### Example

Before:

```text
Commit1
Commit2
Commit3
```

Run:

```bash
git reset --soft HEAD~1
```

Result:

```text
Commit1
Commit2
```

Changes from Commit3 remain staged.

---

## Mixed Reset (Default)

```bash
git reset HEAD~1
```

or

```bash
git reset --mixed HEAD~1
```

Removes commit.

Keeps file changes.

Removes staging.

---

## Hard Reset

```bash
git reset --hard HEAD~1
```

Removes:

* commit
* staging
* changes

Everything gone.

---

### Real-Time Example

#### Soft

```text
Undo submission
Keep paper ready
```

#### Mixed

```text
Undo submission
Take paper back
```

#### Hard

```text
Destroy paper completely
```

---

# 41. Git Revert

Safer than reset.

Creates a new commit that undoes changes.

---

### Command

```bash
git revert commit-id
```

Example:

```bash
git revert a12bc34
```

---

## Before

```text
A → B → C
```

Revert C:

```text
A → B → C → D
```

D reverses C.

---

## Why Companies Prefer Revert

History remains intact.

Safe for shared repositories.

---

# Reset vs Revert

| Reset                | Revert             |
| -------------------- | ------------------ |
| Removes history      | Preserves history  |
| Risky                | Safe               |
| Local use            | Team use           |
| Changes commit graph | Creates new commit |

---

# 42. Squash Commits

Suppose history:

```text
Added login page
Fixed typo
Fixed button
Fixed color
Fixed spacing
```

Messy.

---

## Squash

Combine into:

```text
Added login feature
```

Cleaner history.

---

### Why?

Code review becomes easier.

---

## Real-Time Example

Instead of submitting:

```text
Draft 1
Draft 2
Draft 3
Draft 4
```

Submit:

```text
Final Report
```

---

# 43. Interactive Rebase

Used to:

* Edit commits
* Rename commits
* Squash commits
* Remove commits

---

### Command

```bash
git rebase -i HEAD~4
```

Git shows:

```text
pick
pick
pick
pick
```

Change:

```text
pick
squash
squash
squash
```

Result:

```text
1 clean commit
```

---

## Real-Time Example

Combining multiple daily notes into one final report.

---

# 44. Git Hooks

Hooks are scripts executed automatically.

Location:

```text
.git/hooks
```

---

## Common Hooks

### pre-commit

Runs before commit.

Example:

```text
Check code formatting
Run tests
```

---

### post-commit

Runs after commit.

---

### pre-push

Runs before push.

---

## Real-Time Example

Security guard checks employee ID before entering office.

Hook checks code before commit.

---

# 45. Git Flow Strategy

Popular branching strategy.

---

## Branches

```text
main
develop
feature/*
release/*
hotfix/*
```

---

### Workflow

```text
main
  |
develop
  |
feature-login
feature-payment
feature-profile
```

---

### Feature Completion

```text
feature-login
      ↓
develop
```

---

### Release

```text
develop
      ↓
release
      ↓
main
```

---

## Used In

Large enterprises.

Example:

Banking projects.

Telecom projects.

Government projects.

---

# 46. Trunk-Based Development

Modern alternative.

Popular at:

* Google
* Meta
* Netflix

---

## Structure

```text
main
```

Only short-lived branches.

---

### Flow

```text
Create Branch
Code
Merge Quickly
Delete Branch
```

---

### Benefit

Less merge conflicts.

Faster delivery.

---

# 47. Branch Protection Rules

Important in companies.

Protect:

```text
main
master
release
```

---

## Rules

Cannot directly push.

Must:

```text
Create PR
Review
Approval
Merge
```

---

## Example

Developer cannot do:

```bash
git push origin main
```

GitHub blocks it.

---

## Why?

Prevent accidental production issues.

---

# 48. Code Review Best Practices

Before approving PR:

### Check Naming

Bad:

```java
int x;
```

Good:

```java
int studentCount;
```

---

### Check Security

Bad:

```java
String password = "admin123";
```

---

### Check Duplication

Avoid repeated code.

---

### Check Performance

Bad:

```java
Database query inside loop
```

---

# Real-Time Example

Like a teacher checking assignments before final submission.

---

# 49. GitHub Actions (CI/CD)

Automates tasks.

---

## Example

When code pushed:

```text
Push
 ↓
Build
 ↓
Run Tests
 ↓
Deploy
```

Automatically.

---

## Simple Workflow

File:

```text
.github/workflows/build.yml
```

Example:

```yaml
name: Build

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: mvn test
```

---

## Real-Time Example

Like an automatic quality inspector.

Whenever code arrives:

```text
Compile
Test
Verify
```

without human involvement.

---

# 50. CI/CD Pipeline

## CI

Continuous Integration

Every code change:

```text
Build
Test
Validate
```

---

## CD

Continuous Delivery/Deployment

Automatically release software.

---

### Flow

```text
Developer
    ↓
Git Push
    ↓
GitHub Actions
    ↓
Build
    ↓
Test
    ↓
Deploy
```

---

## Industry Example

Developer fixes login bug.

```bash
git push
```

Pipeline automatically:

```text
Compile Java
Run Unit Tests
Run Security Checks
Deploy to Server
```

---

# 51. Release Management

Example:

```text
v1.0
v1.1
v1.2
v2.0
```

Git Tags:

```bash
git tag v2.0
git push origin v2.0
```

Used to identify production releases.

---

# 52. Real Industry Workflow (50+ Developers)

```text
Clone Repository
       ↓
Pull Latest Changes
       ↓
Create Feature Branch
       ↓
Develop Code
       ↓
Local Testing
       ↓
Commit Changes
       ↓
Push Branch
       ↓
Create Pull Request
       ↓
Automated CI Checks
       ↓
Peer Review
       ↓
Approval
       ↓
Merge Into Main
       ↓
Deploy to Staging
       ↓
QA Testing
       ↓
Production Release
```

---

# Most Asked Interview Questions

### Q1. Difference Between Reset and Revert?

**Reset:** Removes commits/history.

**Revert:** Creates a new commit that undoes changes.

---

### Q2. What is HEAD?

Pointer to current commit.

---

### Q3. What is Detached HEAD?

HEAD points directly to a commit instead of a branch.

---

### Q4. Why Use Rebase?

To create a cleaner, linear history.

---

### Q5. Why Use Squash?

To combine multiple small commits into one meaningful commit.

---

### Q6. What is Git Flow?

A branching strategy using:

```text
main
develop
feature
release
hotfix
```

---

### Q7. What is CI/CD?

Automated build, testing, and deployment pipeline triggered by Git events.

---

# Git Learning Roadmap (Beginner → Advanced)

```text
Git Basics
   ↓
init/add/commit
   ↓
status/log/diff
   ↓
branches
   ↓
merge
   ↓
conflicts
   ↓
GitHub
   ↓
clone/push/pull
   ↓
pull requests
   ↓
stash
   ↓
rebase
   ↓
reset/revert
   ↓
squash
   ↓
hooks
   ↓
Git Flow
   ↓
CI/CD
   ↓
Enterprise Git Practices
```

After mastering all three parts, you'll have the Git/GitHub knowledge expected from a fresher to mid-level software engineer and be well prepared for most Git interview questions and day-to-day development work.
