# 🧪 Git Remote Rebase Conflict: Local Push Rejected

This repository documents my Git experiment with **remote rebase conflicts**—specifically understanding what happens when you push to a remote branch that's currently checked out in a non-bare repo, and how to resolve it.

---

## 🔍 Scenario

- Remote repo initialized as **non-bare** (has working tree)
- `feature1` branch is **checked out** in remote
- A cloned repo tries to rebase and push back to `feature1`
- Push fails with: `error: refusing to update checked out branch: refs/heads/feature1`

---

## 🧪 Experiment Goal

To understand what happens when you push to a **remote branch** that's currently **checked out in a non-bare repo**, and how to resolve it.

---

## 🧵 Resolution Strategy

1. Go to remote repo and **checkout** `master` (so that `feature1` isn't active)
2. Then retry push from cloned repo — ✅ success

---

## ✅ Key Learning

- Git prevents updates to **checked-out branches** in non-bare remotes for safety
- This is **not an issue** in remote servers like GitHub (which use bare repos)
- When simulating locally, always **switch the remote repo's branch** to something else before pushing

---

## 💻 Commands Log

See `commands_log.txt` for the complete terminal journey.

---

## 📦 Repo Structure

```
remote-rebase-conflict/
├── git_origin/          # Simulated remote repository
├── cloned_repo/         # Local clone acting as a contributor
├── commands_log.txt     # Full log of Git experiment commands
└── README.md           # Explanation of the experiment
```

---

## 🔧 Common Solutions

### Method 1: Switch Remote Branch (Recommended for local testing)
```bash
# In the remote repository
git checkout master  # or any branch other than the target
```

### Method 2: Use Bare Repository (Production approach)
```bash
# Initialize as bare repository
git init --bare remote_repo.git
```

### Method 3: Configure Push Policy (Advanced)
```bash
# In remote repository - allow pushes to checked-out branch
git config receive.denyCurrentBranch updateInstead
```

---

## 📚 Additional Notes

- **Bare repositories** (like those on GitHub/GitLab) don't have this issue since they have no working directory
- This error is a **safety feature** to prevent corruption of the remote's working tree
- Always prefer bare repositories for shared/remote repositories in production environments