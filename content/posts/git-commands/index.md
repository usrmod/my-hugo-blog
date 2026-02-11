
---
title: "Git Commands"  # Hugo archetype templating
date: "2026-02-11T20:17:04+05:30"
draft: true
tags: ["git"]
categories: ["tutorials"]
showToc: true
TocOpen: false
hidemeta: false
comments: false
description: "Quick git course with popular git commands and examples"
summary: "Quickly go through most popular git commands with examples"
canonicalURL: ""         # Example: "https://canonical.url/to/page"
disableShare: false
hideSummary: false      # true = hide the summary/excerpt on list pages
searchHidden: false     # true = hide from search index (if you enabled search)
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
UseHugoToc: true
cover:
  image: ""              # e.g. "cover.png" (with relative:true) or "/img/cover.png" or "https://..."
  alt: ""                # alt text for accessibility
  caption: ""            # caption under the cover image
  relative: true         # true for page bundles; false for absolute/URL paths
  hidden: false          # true = hide cover only on single post page
editPost:
  URL: ""                # e.g. "https://github.com/<user>/<repo>/blob/main/content"
  Text: "Suggest Changes"
  appendFilePath: true
---
<!--more-->
> TLDR;
- Master 40+ Git commands with copy-paste ready examples
- Covers daily workflow, branching, collaboration, and troubleshooting
- Organized by use case for quick reference
---


# Essential Git Commands - Quick Revision Guide

## Setup & Configuration

Configure your Git identity before first use:

```bash
# Set your name and email globally
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# View all config settings
git config --global --list

# Set default branch name to main
git config --global init.defaultBranch main
```

## Repository Initialization

Start tracking a project or grab existing code:

```bash
# Initialize a new Git repository in current directory
git init

# Clone a public repository
git clone https://github.com/username/repo.git

# Clone a private repository via SSH
git clone ssh://git@github.com/username/repo.git

# Clone and rename the directory
git clone https://github.com/username/repo.git my-project
```

## Basic Workflow Commands

The bread and butter of daily Git usage:

```bash
# Check current status of working directory
git status

# Add specific file to staging area
git add filename.txt

# Add all changed files to staging
git add -A
# Alternative: git add .

# Commit staged changes with message
git commit -m "Add user authentication feature"

# Add and commit in one step (tracked files only)
git commit -am "Fix typo in README"

# Amend the last commit (change message or add forgotten files)
git commit --amend -m "Updated commit message"
```

**Verify your commit:**

```bash
git log --oneline
# Output: abc123d Add user authentication feature
```

## Branching Commands

Create parallel development lines:

```bash
# List all local branches (* marks current)
git branch

# List all branches including remote
git branch -a

# Create a new branch
git branch feature/new-feature

# Switch to existing branch
git checkout feature/new-feature
# Modern alternative: git switch feature/new-feature

# Create and switch to new branch in one command
git checkout -b feature/login
# Modern alternative: git switch -c feature/login

# Rename current branch
git branch -m new-branch-name

# Delete a local branch (safe, prevents deleting unmerged)
git branch -d feature/old-feature

# Force delete a branch
git branch -D feature/experimental
```

## Merging & Rebasing

Integrate changes from different branches:

```bash
# Merge specific branch into current branch
git checkout main
git merge feature/new-feature

# Abort a merge if conflicts are too complex
git merge --abort

# Rebase current branch onto main (rewrite history)
git rebase main

# Interactive rebase to edit last 3 commits
git rebase -i HEAD~3

# Continue rebase after resolving conflicts
git rebase --continue

# Abort rebase and return to original state
git rebase --abort
```

**When to use:**
- **Merge**: Preserve complete history, good for shared branches
- **Rebase**: Clean linear history, good for feature branches before merging

## Remote Repository Operations

Sync with GitHub/GitLab/Bitbucket:

```bash
# View configured remote repositories
git remote -v

# Add a remote repository
git remote add origin https://github.com/username/repo.git

# Change remote URL (e.g., switching to SSH)
git remote set-url origin git@github.com:username/repo.git

# Push commits to remote branch
git push origin main

# Push and set upstream tracking (first time)
git push -u origin feature/new-feature

# Push all local branches to remote
git push --all

# Delete a remote branch
git push origin --delete feature/old-feature

# Fetch changes from remote (doesn't merge)
git fetch origin

# Pull changes from remote (fetch + merge)
git pull origin main

# Pull with rebase instead of merge
git pull --rebase origin main
```

## Viewing History & Changes

Inspect what happened:

```bash
# View commit history
git log

# Compact one-line format
git log --oneline

# Show last 5 commits
git log -5

# View commits with file changes
git log --stat

# View commits by specific author
git log --author="John Doe"

# Search commit messages
git log --grep="bug fix"

# Show changes in a specific commit
git show abc123d

# View differences between working directory and staging
git diff

# View differences between staging and last commit
git diff --staged

# Compare two branches
git diff main feature/new-feature

# Show who modified each line of a file
git blame filename.txt
```

## Undoing Changes

Fix mistakes at various stages:

```bash
# Discard changes in working directory (single file)
git checkout -- filename.txt
# Modern alternative: git restore filename.txt

# Unstage a file (keep changes in working directory)
git reset HEAD filename.txt
# Modern alternative: git restore --staged filename.txt

# Undo last commit but keep changes staged
git reset --soft HEAD~1

# Undo last commit and unstage changes
git reset HEAD~1
# Same as: git reset --mixed HEAD~1

# Undo last commit and discard all changes (DANGEROUS)
git reset --hard HEAD~1

# Create new commit that reverses a specific commit
git revert abc123d

# Remove all untracked files
git clean -f

# Remove untracked files and directories
git clean -fd

# Preview what clean would remove
git clean -n
```

## Stashing

Temporarily save work without committing:

```bash
# Stash current changes
git stash

# Stash with descriptive message
git stash save "WIP: working on login form"

# List all stashes
git stash list
# Output:
# stash@{0}: WIP: working on login form
# stash@{1}: On main: emergency bug fix

# Apply most recent stash and keep it in stash list
git stash apply

# Apply specific stash
git stash apply stash@{1}

# Apply most recent stash and remove from stash list
git stash pop

# Delete most recent stash
git stash drop

# Delete specific stash
git stash drop stash@{1}

# Clear all stashes
git stash clear

# Create branch from stash
git stash branch feature/new-work
```

## Tags

Mark specific points in history:

```bash
# Create lightweight tag
git tag v1.0.0

# Create annotated tag with message
git tag -a v1.0.0 -m "Release version 1.0.0"

# List all tags
git tag

# Tag a specific commit
git tag v0.9.0 abc123d

# Push specific tag to remote
git push origin v1.0.0

# Push all tags to remote
git push --tags

# Delete local tag
git tag -d v1.0.0

# Delete remote tag
git push origin --delete v1.0.0

# Checkout specific tag
git checkout v1.0.0
```

## Advanced Commands

For complex scenarios:

```bash
# Cherry-pick specific commit to current branch
git cherry-pick abc123d

# Find commit that introduced a bug using binary search
git bisect start
git bisect bad                 # Current version is bad
git bisect good v1.0.0        # v1.0.0 was good
# Git checks out middle commit, test it, then run:
git bisect good   # or git bisect bad
# Repeat until bug commit is found
git bisect reset  # Exit bisect mode

# View reflog (history of HEAD movements)
git reflog

# Recover deleted commit using reflog
git checkout -b recovery abc123d

# Show which files would be pushed
git diff --stat --cached origin/main

# Create alias for long commands
git config --global alias.st status
git config --global alias.co checkout
# Now use: git st instead of git status
```

## .gitignore Essentials

Tell Git which files to ignore:

Create `.gitignore` file in repository root:

```bash
# Ignore all .log files
*.log

# Ignore node_modules directory
node_modules/

# Ignore all files in temp directory
temp/*

# But track temp/.gitkeep
!temp/.gitkeep

# Ignore .env files (sensitive configs)
.env
.env.local

# Ignore OS files
.DS_Store
Thumbs.db

# Ignore IDE folders
.vscode/
.idea/
```

Apply gitignore to already tracked files:

```bash
# Remove file from Git but keep locally
git rm --cached filename.txt

# Remove entire directory from Git
git rm -r --cached node_modules/

git commit -m "Remove ignored files from tracking"
```

## Common Workflows

### Feature Branch Workflow

```bash
# Start new feature
git checkout main
git pull origin main
git checkout -b feature/user-profile

# Work on feature...
git add .
git commit -m "Add user profile page"

# Update feature branch with latest main
git checkout main
git pull origin main
git checkout feature/user-profile
git rebase main

# Push feature branch
git push -u origin feature/user-profile

# After PR approved, clean up
git checkout main
git pull origin main
git branch -d feature/user-profile
```

### Quick Fix Workflow

```bash
# Emergency bug fix on production
git checkout main
git pull origin main
git checkout -b hotfix/critical-bug

# Fix the bug...
git add .
git commit -m "Fix critical authentication bug"

# Push and merge quickly
git push -u origin hotfix/critical-bug
# Create PR, get approval, merge

# Clean up
git checkout main
git pull origin main
git branch -d hotfix/critical-bug
```

## Troubleshooting Commands

Fix common issues:

```bash
# Accidentally committed to wrong branch
git reset HEAD~1               # Undo commit
git stash                      # Save changes
git checkout correct-branch    # Switch
git stash pop                  # Apply changes
git add .
git commit -m "Correct message"

# Need to undo a push (use with EXTREME caution)
git reset --hard HEAD~1
git push --force origin branch-name

# Forgot to create feature branch, already committed to main
git branch feature/new-work    # Create branch at current position
git reset --hard origin/main   # Reset main to remote state
git checkout feature/new-work  # Continue work on feature

# Fix merge conflict
# Edit conflicting files, look for:
# <<<<<<< HEAD
# Your changes
# =======
# Their changes
# >>>>>>> branch-name
# Remove conflict markers, choose correct version
git add conflicted-file.txt
git commit -m "Resolve merge conflict"

# Accidentally deleted branch before merging
git reflog
# Find the commit hash where branch was
git checkout -b recovered-branch abc123d
```

## Quick Reference Table

| Category | Command | Purpose |
|----------|---------|---------|
| **Setup** | `git config --global user.name "Name"` | Set username |
| **Start** | `git init` | Initialize repository |
| **Start** | `git clone <url>` | Clone repository |
| **Status** | `git status` | Check file status |
| **Stage** | `git add <file>` | Stage specific file |
| **Stage** | `git add -A` | Stage all changes |
| **Commit** | `git commit -m "message"` | Commit with message |
| **Branch** | `git branch` | List branches |
| **Branch** | `git checkout -b <name>` | Create and switch branch |
| **Merge** | `git merge <branch>` | Merge branch into current |
| **Remote** | `git push origin <branch>` | Push to remote |
| **Remote** | `git pull origin <branch>` | Pull from remote |
| **Undo** | `git reset --soft HEAD~1` | Undo last commit, keep changes |
| **Undo** | `git restore <file>` | Discard file changes |
| **Stash** | `git stash` | Save work temporarily |
| **Stash** | `git stash pop` | Apply and remove stash |
| **History** | `git log --oneline` | View compact history |
| **Diff** | `git diff` | Show unstaged changes |

## Pro Tips

- Use `git status` frequently to know what's going on
- Commit early, commit often - small atomic commits are easier to manage
- Write meaningful commit messages: "Add user login" not "updates"
- Pull before you push to avoid conflicts
- Never force push to shared branches (main, develop, etc.)
- Use branches for everything - keep main clean
- Review changes before committing: `git diff`
- Backup before experimenting with reset or rebase
- Learn to read git log: `git log --graph --oneline --all`
