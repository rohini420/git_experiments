# Git Experiment: Rebasing with Upstream

This experiment demonstrates the process of forking a GitHub repository, making a change on a feature branch, and then **rebasing** that branch with the upstream source (original repository) before making a pull request.

---

## ✅ Steps Performed

### 1. Create Rebase Experiment Folder
```bash
mkdir rebase_example
cd rebase_example/
```

### 2. Clone the Forked Repository
```bash
git clone https://github.com/rohini420/learngitthehardway.git
cd learngitthehardway/
```

### 3. Create a New Feature Branch
```bash
git checkout -b feature-hello
```

### 4. Add a Change
```bash
echo "hello from contributor" >> README.md
git add README.md
git commit -m "Added hello message"
```

### 5. Add the Original Repository as Upstream
```bash
git remote add upstream https://github.com/ianmiell/learngitthehardway.git
git fetch upstream
```

### 6. Rebase the Feature Branch with Upstream
```bash
git rebase upstream/master
```

- This resulted in a **merge conflict** in `README.md`.

### 7. Resolve Conflict and Complete Rebase
```bash
# Manually resolved the conflict in README.md using a text editor
git add README.md
git rebase --continue
```

---

## 🧠 What I Learned

- How to rebase a local branch with upstream changes.
- Resolving merge conflicts during a rebase.
- Importance of rebasing before raising a pull request.

---

🕒 Timestamp: 2025-07-16 18:07:23