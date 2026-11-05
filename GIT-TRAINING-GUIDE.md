# Git Basics — Team Training Guide

A hands-on walkthrough of core Git concepts and commands, with practice
files you can use to try everything out safely before touching real
project repos.

---

## 1. What Git Is (in one minute)

Git is a **version control system**: it tracks changes to files over time
so you can see history, undo mistakes, and collaborate without
overwriting each other's work.

Three key areas to remember:

| Area | What it is |
|---|---|
| **Working directory** | The files you see and edit on disk |
| **Staging area (index)** | A holding area for changes you're about to commit |
| **Repository (.git)** | The permanent history of committed snapshots |

```
Working directory  --(git add)-->  Staging area  --(git commit)-->  Repository
```

---

## 2. One-time Setup

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

Check your settings any time:

```bash
git config --list
```

---

## 3. Starting a Repository

| Command | Purpose |
|---|---|
| `git init` | Turn the current folder into a new Git repo |
| `git clone <url>` | Copy an existing remote repo to your machine |

```bash
git init
git clone git@github.com:org/repo.git
```

---

## 4. The Everyday Loop: status, add, commit

| Command | Purpose |
|---|---|
| `git status` | See what's changed, staged, or untracked |
| `git add <file>` | Stage a specific file |
| `git add .` | Stage everything changed in the current folder |
| `git commit -m "message"` | Save staged changes as a new snapshot |
| `git commit -am "message"` | Stage + commit tracked file changes in one step |

```bash
git status
git add notes.txt
git commit -m "Add initial notes"
```

**Good commit messages**: short summary line (imperative mood, e.g. "Add
login form"), blank line, then details if needed.

---

## 5. Looking at History

| Command | Purpose |
|---|---|
| `git log` | Full commit history |
| `git log --oneline` | Compact one-line-per-commit view |
| `git log --oneline --graph --all` | Visual branch history |
| `git show <commit>` | Details of one commit |
| `git diff` | Unstaged changes vs. last commit |
| `git diff --staged` | Staged changes vs. last commit |

---

## 6. Undoing Things

| Command | Purpose |
|---|---|
| `git restore <file>` | Discard uncommitted changes in a file |
| `git restore --staged <file>` | Unstage a file (keep the edits) |
| `git commit --amend` | Edit the most recent commit (message or contents) |
| `git revert <commit>` | Create a new commit that undoes an old one (safe for shared history) |
| `git reset --hard <commit>` | Rewind the branch and discard changes (⚠️ destructive, local only) |

> Rule of thumb: use `revert` on shared/pushed branches, `reset` only on
> commits nobody else has pulled yet.

---

## 7. Branching

| Command | Purpose |
|---|---|
| `git branch` | List local branches |
| `git branch <name>` | Create a new branch |
| `git switch <name>` | Switch to a branch |
| `git switch -c <name>` | Create and switch in one step |
| `git checkout <name>` | Older syntax, same as switch |
| `git branch -d <name>` | Delete a branch (safe, blocks unmerged work) |
| `git branch -D <name>` | Force-delete a branch |

```bash
git switch -c feature/add-login
# ...make changes, commit...
git switch main
```

---

## 8. Merging & Resolving Conflicts

| Command | Purpose |
|---|---|
| `git merge <branch>` | Merge another branch into your current branch |
| `git mergetool` | Open a configured tool to resolve conflicts |

When a conflict happens, Git marks the file like this:

```
<<<<<<< HEAD
your current branch's version
=======
the other branch's version
>>>>>>> feature/add-login
```

Steps to resolve:
1. Open the file, decide what the final content should be.
2. Remove the `<<<<<<<`, `=======`, `>>>>>>>` markers.
3. `git add <file>`
4. `git commit` to complete the merge.

---

## 9. Working with Remotes

| Command | Purpose |
|---|---|
| `git remote -v` | List configured remotes |
| `git fetch` | Download remote changes without merging |
| `git pull` | Fetch + merge (or rebase) in one step |
| `git push` | Upload your commits |
| `git push -u origin <branch>` | Push a new branch and track it |

```bash
git fetch origin
git pull origin main
git push origin feature/add-login
```

---

## 10. Stashing (temporary shelf for changes)

| Command | Purpose |
|---|---|
| `git stash` | Save uncommitted changes and clean the working directory |
| `git stash list` | See stashed entries |
| `git stash pop` | Re-apply the most recent stash and remove it from the list |
| `git stash drop` | Discard a stash without applying it |

Use this when you need to switch branches quickly without committing
half-finished work.

---

## 11. Ignoring Files

Create a `.gitignore` file listing patterns Git should never track
(build output, dependencies, secrets, OS files):

```
node_modules/
.env
*.log
```

---

## 12. Quick Reference Cheat Sheet

```
git init                      start a new repo
git clone <url>                copy a remote repo
git status                     what's changed
git add <file> | .             stage changes
git commit -m "msg"            save a snapshot
git log --oneline --graph --all  view history
git diff / git diff --staged   see changes
git switch -c <name>           create + switch branch
git merge <branch>             merge a branch in
git fetch / git pull / git push  sync with remote
git stash / git stash pop      shelve work temporarily
git restore <file>             discard local changes
git revert <commit>            undo a commit safely
```

---

## 13. How to Use the Practice Files

See [`practice-files/EXERCISES.md`](practice-files/EXERCISES.md) for a
guided sequence of exercises using the sample text files in this repo —
covering commits, branching, merging, and a deliberate merge conflict to
resolve together as a team.
