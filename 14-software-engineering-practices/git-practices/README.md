# Git and Collaboration Practices

Git is not optional for a professional developer. It is the tool that makes collaboration, code review, and safe deployment possible. This document covers the Git workflows and conventions used at AmaliTech.

---

## 1. Git Basics

### Core Commands

```bash
# Initialize a new repository
git init

# Clone an existing repository
git clone https://github.com/org/repo.git

# Check the current state of the working tree
git status

# Stage changes (prepare them for the next commit)
git add <file>          # stage a specific file
git add .               # stage all changed files

# Commit staged changes with a message
git commit -m "feat: add employee search endpoint"

# Push commits to the remote repository
git push origin main

# Pull the latest changes from the remote
git pull origin main

# View the commit history
git log
git log --oneline       # compact view
git log --oneline --graph --all  # visual branch diagram
```

### What Happens When You Commit

```
Working Directory  →  Staging Area  →  Local Repository  →  Remote Repository
     (edit files)      (git add)         (git commit)           (git push)
```

Each commit is a snapshot of the entire project at a point in time, identified by a SHA hash. Git never deletes commits — it only adds to history.

---

## 2. Branching

### Why Branch?

The `main` branch should always contain code that works. When you are building a feature or fixing a bug, you work on a separate branch. When the work is reviewed and tested, you merge it into `main`.

```bash
# Create a branch and switch to it
git checkout -b feature/employee-search

# Switch between existing branches
git checkout main
git checkout feature/employee-search

# Modern equivalent (Git 2.23+)
git switch -c feature/employee-search   # create and switch
git switch main                          # switch

# List branches
git branch        # local branches
git branch -a     # local and remote branches

# Push a new branch to the remote
git push -u origin feature/employee-search

# Delete a branch after merging
git branch -d feature/employee-search        # local
git push origin --delete feature/employee-search  # remote
```

### Branch Naming Conventions

| Prefix | Purpose | Example |
|--------|---------|---------|
| `feature/` | New functionality | `feature/employee-search` |
| `fix/` | Bug fixes | `fix/login-redirect-loop` |
| `docs/` | Documentation only | `docs/api-setup-guide` |
| `refactor/` | Code restructuring, no behavior change | `refactor/extract-payment-service` |
| `test/` | Adding or fixing tests | `test/task-service-unit-tests` |
| `chore/` | Maintenance: dependencies, config | `chore/upgrade-spring-boot-3.3` |
| `hotfix/` | Urgent fix for production | `hotfix/null-pointer-in-checkout` |

Names should be lowercase with hyphens, not spaces or underscores. Keep them short but descriptive.

---

## 3. Commit Message Conventions — Conventional Commits

A good commit message tells the reader **what changed and why**, not how. AmaliTech follows the [Conventional Commits](https://www.conventionalcommits.org/) specification.

### Format

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

### Types

| Type | When to use |
|------|------------|
| `feat` | A new feature visible to users or callers |
| `fix` | A bug fix |
| `docs` | Documentation only (README, Javadoc) |
| `refactor` | Code restructuring with no behavior change |
| `test` | Adding or modifying tests |
| `chore` | Maintenance: dependencies, build config, CI |
| `style` | Formatting, whitespace, semicolons — no logic change |
| `perf` | Performance improvement |
| `ci` | CI/CD configuration changes |

### Examples

```bash
# Simple one-line commit
git commit -m "feat: add search endpoint for employees by department"

# Bug fix with scope
git commit -m "fix(auth): correct token expiry check to use UTC"

# Commit that explains the why
git commit -m "refactor(task): extract status validation into TaskValidator

The status transition logic was duplicated in TaskService and TaskController.
Extracting it ensures one authoritative place to change rules."

# Breaking change — note the BREAKING CHANGE footer
git commit -m "feat(api)!: rename /employees to /staff

BREAKING CHANGE: All client calls to /api/employees must update to /api/staff.
The old path is removed as of this commit."
```

### What Makes a Good Commit Message

- **Subject line under 72 characters**
- **Imperative mood**: "add" not "added" or "adds" — as if completing the sentence "If applied, this commit will..."
- **Subject describes the what**: `feat: add pagination to employee list`
- **Body describes the why**: explain the motivation, not the mechanics
- **One logical change per commit**: do not bundle unrelated changes

### What Not to Do

```bash
# BAD — vague and useless
git commit -m "fix stuff"
git commit -m "WIP"
git commit -m "asdf"
git commit -m "changes"

# BAD — describes the how, not the what/why
git commit -m "changed the if statement to also check for null"
```

---

## 4. Pull Requests

A Pull Request (PR) is a request to merge your branch into `main` (or another target branch). It is the central unit of code review.

### Writing a Good PR Description

```markdown
## What this PR does
Adds a search endpoint for employees: GET /api/employees/search?name=Alice&department=Engineering

## Why
Clients were requesting all employees and filtering client-side, which transfers too much data
over the network. Server-side search fixes this.

## Changes
- Added `EmployeeSearchController` with GET `/api/employees/search`
- Added `EmployeeSearchService` with query methods
- Extended `EmployeeRepository` with two new finder methods
- Added unit tests for all new components

## How to test
1. Start the application: `mvn spring-boot:run`
2. POST a few employees: `curl -X POST http://localhost:8080/api/employees -d '...'`
3. Test search: `curl 'http://localhost:8080/api/employees/search?name=Alice'`

## Related
Closes #42
```

### PR Checklist Before Requesting Review

- [ ] All tests pass locally (`mvn test`)
- [ ] No compilation warnings
- [ ] New code has unit tests
- [ ] No debugging code, `System.out.println`, or commented-out code
- [ ] PR description explains what and why
- [ ] Branch is up to date with `main` (rebased or merged)

### Keeping PRs Small

Large PRs are harder to review and more likely to introduce bugs. Aim for PRs that do one thing. If a feature is large, split it into separate PRs (foundation, feature, tests, docs).

---

## 5. Code Review

### What to Look For as a Reviewer

**Correctness:**
- Does the code do what the PR says it does?
- Are edge cases handled? (null inputs, empty lists, concurrent access)
- Are exceptions handled or propagated correctly?

**Amalitech Coding Standards:**
- Do names follow §3 conventions?
- Do functions follow §4 (small, one thing, no side effects)?
- Does the code follow §12 SOLID principles?
- Is formatting consistent with §7?

**Security:**
- Is user input validated before use?
- Are SQL queries parameterized (no string concatenation)?
- Are passwords/secrets handled correctly?
- Are authorization checks in place?

**Tests:**
- Are the tests meaningful? (Do they assert behavior, or do they just run code?)
- Are edge cases tested?
- Could any test be made clearer?

### How to Write Review Comments

**Be specific, not vague:**

```
# BAD
"This is not clean."

# GOOD
"This method is 60 lines. Per §4.1, methods should rarely exceed 20 lines.
The validation logic (lines 15–30) could be extracted into validateInput()."
```

**Explain the reason:**

```
# BAD
"Use PreparedStatement here."

# GOOD
"Building the SQL string with concatenation on line 24 is vulnerable to SQL injection.
Use PreparedStatement with ? placeholders instead — see the JDBC chapter for the pattern."
```

**Distinguish blocking issues from suggestions:**

Use clear labels:
- `[blocking]` — Must be fixed before merge
- `[suggestion]` — Worth considering, not required
- `[question]` — I do not understand this; can you explain?
- `[nit]` — Minor style issue, feel free to ignore

### Responding to Review Comments

- Read every comment before replying to any.
- If you agree, make the change and reply "Done."
- If you disagree, explain your reasoning politely. The reviewer may not have context you have.
- If a comment is unclear, ask for clarification before guessing what they want.
- Never push back with "that's just your opinion." If the standard says otherwise, cite it.

---

## 6. .gitignore

`.gitignore` tells Git which files to ignore. Never track:

```gitignore
# Compiled output
target/
*.class
*.jar

# IDE files
.idea/
*.iml
.vscode/
*.swp

# Environment and secrets — NEVER commit these
.env
*.env
application-local.properties
secrets.properties

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/
```

### Never Commit Secrets

Credentials, API keys, passwords, and tokens must never appear in a Git repository — not even in a private one.

If you accidentally commit a secret:
1. Immediately revoke or rotate the credential.
2. Remove it from the file and commit again (this is not enough — it is still in history).
3. Use `git filter-branch` or the BFG Repo Cleaner to purge it from history.
4. Force-push the cleaned history (coordinate with your team).

The correct approach is to load secrets from environment variables:

```java
// BAD — secret in code
private static final String API_KEY = "sk-1234abcd";

// GOOD — load from environment
private static final String API_KEY = System.getenv("PAYMENT_API_KEY");
```

Or use `application.properties` with environment variable substitution in Spring Boot:

```properties
payment.api.key=${PAYMENT_API_KEY}
```

---

## 7. Branching Strategy

The standard branching strategy for most AmaliTech projects:

```
main          ← production-ready code; protected; no direct commits
  ↑
develop       ← integration branch; all features merge here first
  ↑
feature/xxx   ← your work; branches from develop, merges back to develop
fix/xxx       ← bug fixes; branches from develop (or main for hotfixes)
```

For simpler projects (early-stage or small teams), trunk-based development works:

```
main          ← everyone works here via short-lived feature branches
  ↑
feature/xxx   ← branch; complete in 1–2 days; merge via PR
```

### Key Rules

1. `main` is always deployable. If a commit breaks `main`, roll it back immediately.
2. Never commit directly to `main` or `develop`. Always use a PR.
3. Keep branches short-lived. A branch older than a week is a risk.
4. Rebase or merge `main` into your branch before opening a PR, to resolve conflicts early.

---

## Summary

| Practice | Rule |
|----------|------|
| Branch naming | `feature/`, `fix/`, `docs/`, `refactor/`, `test/`, `chore/` |
| Commit messages | Conventional Commits: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:` |
| Commit size | One logical change per commit |
| PRs | Small, focused; include description of what and why |
| Code review | Specific, reasoned feedback; cite standards |
| Secrets | Never in code or Git history; use environment variables |
| .gitignore | Track `target/`, IDE files, `.env` files, logs |
