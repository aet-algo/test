# Complete Git Setup & Troubleshooting Guide

A comprehensive blueprint for initializing, linking, and managing local and remote Git repositories.

---

## 🚀 Scenario 1: Initializing a Brand New Local Project & Pushing to GitHub
Use this path when you have an existing or brand-new folder of local code on your machine that you want to upload to GitHub for the first time.

### 1. Initialize Git Locally
Open your terminal, navigate to your project directory, and initialize a local Git repository:
```bash
git init
```

### 2. Stage and Commit Your Local Files
Track all files currently inside your directory and log your initial save state:
```bash
git add .
git commit -m "Initial commit of local files"
```

### 3. Link Your Remote Repository
Connect your local workspace to your target GitHub repository path:
```bash
git remote add origin https://github.com
```

### 4. Explicitly Set the Default Branch Name
Ensure your active working branch is explicitly named `main` (matching GitHub's current naming architecture):
```bash
git branch -M main
```

### 5. Reconcile Existing Files (Crucial Step)
If your GitHub repository was created with an automated `.gitignore` or `README.md`, you **must** reconcile the history gap before pushing, otherwise your transmission will fail (`non-fast-forward` error).

Define your pull reconciliation behavior strategy:
```bash
git config pull.rebase false
```
Safely pull down the mismatched remote assets into your empty directory:
```bash
git pull origin main --allow-unrelated-histories
```

### 6. Publish Code Upstream
Push your local commits and link your upstream tracking branch:
```bash
git push -u origin main
```

---

## 🔒 Scenario 2: Public vs. Private Repository Authentication Setup
When working with different visibility permissions on GitHub, the core commands remain identical, but authentication parameters differ.

### Case A: Public Repositories
* **Behavior:** Visible to anyone on the web. Anyone can view or clone the code, but only explicitly invited contributors can push updates.
* **Authentication:** A standard secure handshake is required when executing a `push`. You can use your HTTPS repository link and log in when prompted.

### Case B: Private Repositories
* **Behavior:** Visible exclusively to you and explicitly invited collaborators. Unauthenticated clone or push queries will throw an `Error: Repository not found`.
* **Authentication Options:** Modern GitHub security architecture **does not accept plain account passwords** on the terminal command line. You must use one of two methods:

#### Method 1: Personal Access Tokens (PAT)
1. Generate a token via GitHub: `Settings` ➡️ `Developer Settings` ➡️ `Personal Access Tokens` ➡️ `Tokens (classic)`.
2. Check the box for `repo` scope permissions.
3. Copy the generated token string.
4. When your terminal asks for your **Password** during a git push/pull command, paste this **Token string** instead.

#### Method 2: SSH Protocol (Recommended)
Avoid entering tokens entirely by switching your remote origin link from HTTPS to SSH.
1. Check if you have an SSH key pair or generate one: `ssh-keygen -t ed25519 -C "your_email@example.com"`.
2. Add your public key (`~/.ssh/id_ed25519.pub`) to your GitHub account under `Settings` ➡️ `SSH and GPG keys`.
3. Update your local repository's remote path to use the SSH link format:
   ```bash
   git remote set-url origin git@github.com:username/repository-name.git
   ```

---

## 🔄 Daily Maintenance Workflow Reference

Once initialization and authentication are settled, use this rapid-fire sequence for daily updates:

```bash
# 1. Pull changes down to stay fresh
git pull

# 2. Track all your new local creations or modifications
git add .

# 3. Create a descriptive save point
git commit -m "Update core features"

# 4. Push updates instantly (Upstream tracking is already saved)
git push
```
