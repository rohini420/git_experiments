# Git Squash Experiment - Step by Step

This guide walks you through a complete Git squash experiment, demonstrating how to combine multiple commits into a single commit using interactive rebase.

## Overview

We'll create a repository with 10 commits and then squash them all into a single commit. This is useful for cleaning up commit history before merging features or preparing releases.

## Prerequisites

- Git installed and configured
- Basic understanding of Git commands
- Terminal/command line access

## Step-by-Step Instructions

### Step 1: Setup a New Repository

Create a new folder and initialize a Git repository:

```bash
mkdir squashing_scenario
cd squashing_scenario
git init
```

### Step 2: Create Initial File and Commit

Create an empty file and make the initial commit:

```bash
touch afile
git add afile
git commit -m "initial commit"
```

### Step 3: Create 10 Additional Commits

Use a loop to quickly create 10 commits by appending numbers to the file:

```bash
for i in {1..10}; do echo $i >> afile; git commit -am "commit:$i"; done
```

Verify the commits were created:

```bash
git log --oneline
```

You should see something like:

```
abc1234 commit:10
def5678 commit:9
...
xyz7890 commit:1
initial123 initial commit
```

### Step 4: Start Interactive Rebase

Begin an interactive rebase from the root commit:

```bash
git rebase -i $(git rev-list --max-parents=0 HEAD) HEAD
```

This will open your default editor with a list of commits. Modify the file to squash commits:

- Keep the first commit as `pick`
- Change all other commits from `pick` to `squash` (or `s`)

Example:
```
pick <commit-id-1> commit:1
squash <commit-id-2> commit:2
squash <commit-id-3> commit:3
squash <commit-id-4> commit:4
squash <commit-id-5> commit:5
squash <commit-id-6> commit:6
squash <commit-id-7> commit:7
squash <commit-id-8> commit:8
squash <commit-id-9> commit:9
squash <commit-id-10> commit:10
```

Save and exit the editor.

### Step 5: Resolve Conflicts (If Any)

If you encounter merge conflicts:

```
CONFLICT (content): Merge conflict in afile
```

Follow these steps:

1. Open the conflicted file in your editor:
   ```bash
   vi afile   # or use your preferred editor
   ```

2. Manually resolve the conflicts by editing the file content

3. Stage the resolved file:
   ```bash
   git add afile
   ```

4. Continue the rebase:
   ```bash
   git rebase --continue
   ```

5. Repeat steps 1-4 until all conflicts are resolved

### Step 6: Reattach Master Branch

After the rebase is complete, reattach the master branch to the new squashed commit:

```bash
git branch -f master
git checkout master
```

### Step 7: Verify the Final State

Check that the squash was successful:

```bash
git log --oneline
```

You should now see only 2 commits:

```
<new-squashed-commit-id> commit:10
<initial-commit-id> initial commit
```

## Success Criteria

✅ Repository contains only 2 commits total
✅ All changes from the 10 individual commits are preserved
✅ Commit history is clean and consolidated

## Troubleshooting

### Common Issues

**Editor doesn't open during rebase:**
- Set your Git editor: `git config --global core.editor "nano"` (or vim, code, etc.)

**Rebase gets stuck:**
- Abort and restart: `git rebase --abort`
- Check for uncommitted changes: `git status`

**Lost commits:**
- Use `git reflog` to find lost commits
- Reset to a previous state if needed

### Useful Commands

- Check current rebase status: `git status`
- Abort rebase: `git rebase --abort`
- Skip a commit during rebase: `git rebase --skip`
- View detailed log: `git log --graph --oneline --all`

## Next Steps

Consider adding these workflows:

- Push to remote repository (may require force push)
- Create pull request with clean commit history
- Set up branch protection rules
- Implement commit message conventions

## Notes

- **Warning:** Squashing rewrites Git history. Only do this on branches that haven't been shared publicly
- Force push may be required when pushing squashed commits to remote repositories
- Consider using `git rebase -i HEAD~n` for squashing the last n commits instead of from root
