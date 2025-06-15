# 🚀 Git Command Overview

A practical and concise guide to commonly used and useful Git commands, structured for everyday development, collaboration, and troubleshooting.

---

## 🔰 Getting Started

### Initialize a New Local Repository

```bash
git init    # Starts version control in your current directory (creates .git folder)
```

### Clone a Remote Repository (using GitHub Token)

```bash
git clone https://<token>@github.com/username/repo-name.git  # Clones a repo and sets origin remote with authentication via token
```

---

## 💻 Local Workflow

### Check Repo Status

```bash
git status    # Shows staged, unstaged, and untracked files
```

### Stage Files

```bash
git add <file>      # Stage specific file
git add .           # Stage all changes
```

### Commit Changes

```bash
git commit -m "Commit message"                  # Save staged changes with a message
git commit --amend -m "Updated message"         # Modify last commit (if not pushed)
```

### Unstage a File

```bash
git reset <file>  # Removes file from staging area but keeps local changes
```

### Discard Changes

```bash
git restore <file>       # Restore single file to last commit
git restore .            # Restore all files to last commit
git checkout -- <file>   # Legacy alternative to restore a file
```

### View Changes

```bash
git diff               # View unstaged changes
git diff --staged      # View staged changes
git diff HEAD          # View all changes since last commit
```

### View Commit History

```bash
git log                # Full commit history
git log --oneline      # Compact view of commit history
```

---

## 🌐 Remote Repositories

### Add Remote

```bash
git remote add origin https://github.com/username/repo.git  # Add remote repository named 'origin'
```

### Push Commits

```bash
git push -u origin main  # First push, sets upstream
git push                 # Push subsequent commits
```

### Pull Changes

```bash
git pull  # Fetches and merges remote changes into current branch
```

### Fetch Changes (without merge)

```bash
git fetch               # Get changes from remote without merging
git fetch --prune       # Remove remote-tracking branches that no longer exist
```

---

## 🌿 Branching & Collaboration

### List Branches

```bash
git branch             # Show local branches
git branch -r          # Show remote branches
git branch -a          # Show all branches
```

### Create a New Branch

```bash
git branch feature-xyz  # Create a new branch called feature-xyz
```

### Switch to a Branch

```bash
git switch feature-xyz       # Switch to branch (modern)
git checkout feature-xyz     # Switch to branch (legacy)
```

### Create and Switch in One Command

```bash
git switch -c feature-xyz        # Create & switch (modern)
git checkout -b feature-xyz      # Create & switch (legacy)
```

### Switch and Track a Remote Branch Locally

```bash
git switch --track origin/feature-xyz            # Modern
git checkout -b feature-xyz origin/feature-xyz   # Legacy
```

### Merge a Branch into Current

```bash
git merge feature-xyz   # Merge feature-xyz into current branch
```

### Delete a Branch

```bash
git branch -d feature-xyz       # Safe delete (if already merged)
git branch -D feature-xyz       # Force delete
```

---

## 📦 Stashing Work

### Stash Changes

```bash
git stash  # Temporarily save changes without committing
```

### List Stashes

```bash
git stash list  # View saved stashes
```

### Apply Most Recent Stash (keep in stash)

```bash
git stash apply  # Apply latest stash but keep it in the list
```

### Apply and Remove Most Recent Stash

```bash
git stash pop  # Apply and remove latest stash from list
```

### Remove a Specific Stash

```bash
git stash drop stash@{0}  # Delete specific stash by index
```

---

## 🔧 Advanced & Useful Commands

### Visualize Commit Graph

```bash
git log --oneline --graph --all  # View commit tree across branches
```

### Remove a File from Git But Keep Locally

```bash
git rm --cached <file>  # Stop tracking a file but keep it locally
```

### Tag a Version

```bash
git tag v1.0.0          # Create tag locally
git push origin v1.0.0  # Push tag to remote
```

### Ignore Changes to a Tracked File (Locally)

```bash
git update-index --assume-unchanged <file>  # Temporarily ignore file changes
```

### Start Tracking Ignored File Again

```bash
git update-index --no-assume-unchanged <file>  # Start tracking changes again
```

### Skip Worktree (useful for config/env files in local dev)

```bash
git update-index --skip-worktree <file>  # Don't touch this file during merges/pulls
```

### Unset Skip Worktree

```bash
git update-index --no-skip-worktree <file>  # Resume normal tracking
```

### Restore All Working Files to Last Commit

```bash
git restore .  # Reset all changes in working directory
```

---

## 🧼 Cleanup

### Delete Local Branch That No Longer Exists on Remote

```bash
git fetch --prune  # Clean up local references to deleted remote branches
```

### Delete All Local Branches Gone from Remote (advanced)

```bash
git remote prune origin  # Aggressively clean up remote-tracking branches
```

Use with caution — removes all remote-tracking branches no longer on the remote.
