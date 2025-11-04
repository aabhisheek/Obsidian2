
Good question 👍 — here’s a clean step-by-step guide to **pull a PR branch (like `feature/comp_off` from PR #74)** into your **local machine (VS Code)** using **Git**.

---

### 🧩 Case 1 — The PR branch **exists in the remote repo** (like `origin/feature/comp_off`)

#### 🪜 Steps

1. **Open terminal in VS Code**
    
    ```
    Ctrl + `
    ```
    
    or  
    From menu: **View → Terminal**
    
2. **Check current branch and remote**
    
    ```bash
    git status
    git remote -v
    ```
    
    You’ll see something like:
    
    ```
    origin  https://github.com/orgname/repo.git (fetch)
    origin  https://github.com/orgname/repo.git (push)
    ```
    
3. **Fetch all remote branches**
    
    ```bash
    git fetch origin
    ```
    
4. **List branches to confirm it exists**
    
    ```bash
    git branch -a
    ```
    
    Look for:
    
    ```
    remotes/origin/feature/comp_off
    ```
    
5. **Checkout that remote branch locally**
    
    ```bash
    git checkout -b feature/comp_off origin/feature/comp_off
    ```
    
    ✅ This creates a local branch named `feature/comp_off` tracking the remote one.
    
6. **Pull latest changes (just to be sure)**
    
    ```bash
    git pull
    ```
    

Now your local branch is **exactly the same as PR branch #74**.

---

### 🧩 Case 2 — The PR is from **someone else’s fork** (e.g., not in your `origin`)

Then you need to **fetch it from their fork** directly.

#### 🪜 Steps

1. Go to the PR page on GitHub (e.g. `https://github.com/org/repo/pull/74`)
    
2. Note the **source repo and branch** — it’ll say something like:
    
    ```
    user123:feature/comp_off  ←  orgname:develop
    ```
    
3. Add that fork as a **temporary remote**
    
    ```bash
    git remote add user123 https://github.com/user123/repo.git
    ```
    
4. Fetch that specific branch
    
    ```bash
    git fetch user123 feature/comp_off
    ```
    
5. Checkout it locally
    
    ```bash
    git checkout -b feature/comp_off user123/feature/comp_off
    ```
    
6. Optional: Remove the remote later
    
    ```bash
    git remote remove user123
    ```
    

---

### ✅ Verify

```bash
git branch
# should show *feature/comp_off

git log --oneline -5
# to see recent commits from that PR
```

---

Would you like me to show the **exact commands** for your case (e.g., your GitHub org/repo name and PR link)?  
If you paste the PR link (e.g., `https://github.com/org/repo/pull/74`), I’ll give you the **ready-to-run commands**.