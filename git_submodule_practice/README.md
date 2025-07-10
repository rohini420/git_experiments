# 🧪 Git Submodule Practice

This repository documents my Git experiments with **submodules**—from creating and linking submodules to cloning projects that include submodules. These are part of my foundational Git learning journey.

---

## ✅ Lesson 1: Creating and Using a Submodule

### 🧩 Objective
Learn how to:
- Create a Git repo (`rohinilib`) as a submodule.
- Add it to another repo (`remo_repo`).
- Track its branch and commit updates inside the parent repo.

### 🛠️ Steps I Performed

1. **Initialize submodule repo** (`rohinilib`)
   ```bash
   mkdir rohinilib && cd rohinilib
   git init
   echo "A" > file1
   git add file1
   git commit -m "Initial commit with file1"
   git checkout -b experimental
   echo "C - EXPERIMENTAL" >> file1
   git commit -am "Experimental update"
   cd ..
   ```

2. **Create parent repo** (`remo_repo`) and add the submodule
   ```bash
   mkdir remo_repo && cd remo_repo
   git init
   touch main_script.sh
   git add main_script.sh
   git commit -m "Initial commit with main script"
   git submodule add ../rohinilib
   git commit -am "Added rohinilib as submodule"
   cd ..
   ```

3. **Verify submodule is tracked**
   - `git submodule status` shows the commit hash of `rohinilib`
   - `.gitmodules` file is auto-created with path and URL to the submodule

---

## ✅ Lesson 2: Cloning a Project That Has Submodules

### 🤯 The Problem
If someone clones your main repo normally, they'll get the submodule folder but it will be **empty** unless explicitly initialized and updated.

### 🧪 Case 1: Cloning Normally
```bash
git clone remo_repo remo_repo_cloned
cd remo_repo_cloned
git submodule init
git submodule update
```
This gets the submodule contents **after** initialization.

### ✅ Case 2: Cloning With `--recursive` (Recommended)
```bash
git clone --recursive remo_repo remo_repo_cloned_recursive
```
This **automatically pulls** the submodule and checks out the correct commit. Much easier and safer when sharing projects!

---

## 📘 My Learnings

- Submodules **lock to a commit**, not to a branch. You have to explicitly update them in the parent repo to track new changes.
- `.gitmodules` is a config file that tracks submodule paths and URLs.
- Use `--recursive` while cloning to save time and confusion.
- `git submodule update` pulls the exact commit referenced by the main project.
- `git submodule status` shows you which commit your submodule is pointing to.

---

## 🗂️ Folder Structure

```
git_submodule_practice/
├── command_log.txt
├── README.md
├── remo_repo/
│   ├── main_script.sh
│   └── rohinilib/ (as submodule)
├── remo_repo_cloned/
├── remo_repo_cloned_recursive/
└── rohinilib/
```

---

## 📍 Next Steps

- Practice pushing and pulling submodules across remotes
- Explore `git submodule foreach` and nested submodules
- Learn how to keep submodules synced with upstream changes