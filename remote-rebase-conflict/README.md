# Git Remote Rebase Conflict: Local Push Rejected

## 🧪 Experiment Goal

To understand what happens when you push to a **remote branch** that's currently **checked out in a non-bare repo**, and how to resolve it.

---

## 🔍 Scenario

- Remote repo initialized as **non-bare** (has working tree)
- `feature1` branch is **checked out** in remote
- A cloned repo tries to rebase and push back to `feature1`
- Push fails with:  
  `error: refusing to update checked out branch: refs/heads/feature1`

---

## 🧵 Resolution Strategy

1. Go to remote repo and **checkout `master`** (so that `feature1` isn't active)
2. Then retry push from cloned repo — ✅ success

---

## ✅ Key Learning

- Git prevents updates to **checked-out branches** in non-bare remotes for safety
- This is **not an issue** in remote servers like GitHub (which use bare repos)
- When simulating locally, always **switch the remote repo's branch** to something else before pushing

---

## 💻 Commands Log

See [`commands_log.txt`](./commands_log.txt) for the complete terminal journey.

---

## 📦 Repo Structure

remote-rebase-conflict/
├── git_origin/ # Simulated remote repository
├── cloned_repo/ # Local clone acting as a contributor
├── commands_log.txt # Full log of Git experiment commands
└── README.md # Explanation of the experiment
