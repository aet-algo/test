# Git Workflow Guide

This guide outlines the essential Git commands for keeping your local repository synchronized with the remote GitHub repository.

## Everyday Development Workflow

Follow these steps for daily development tasks to prevent conflicts and keep your project updated.

### 1. Synchronize Local Code
Before modifying files, pull any remote updates (e.g., changes made via the GitHub web interface or by collaborators).
```bash
git pull
```

### 2. Stage Changes
After modifying, adding, or deleting local files, stage your adjustments for tracking.
```bash
git add .
```

### 3. Commit Changes
Save your staged changes to your local commit history with a descriptive log message.
```bash
git commit -m "Your descriptive commit message here"
```

### 4. Publish to GitHub
Upload your local commits to the remote repository. 
```bash
git push
```
*Note: The `-u origin main` configuration flag is no longer required as your upstream tracker is now established.*

---

## Branch Management

Isolate experimental code or new features safely using separate branches.

### Create and Switch to a New Branch
```bash
git checkout -b feature-branch-name
```

### Switch Back to Main Branch
```bash
git checkout main
```

### Merge Feature Changes into Main
1. Return to the main branch: `git checkout main`
2. Run the merge command:
```bash
git merge feature-branch-name
```
