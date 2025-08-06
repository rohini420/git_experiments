# Git Cherry-Pick and Three-Way Merge

A comprehensive guide to understanding Git cherry-pick, three-way merges, and patch application workflows based on practical exploration and testing.

## Table of Contents
- [Overview](#overview)
- [Understanding Cherry-Pick](#understanding-cherry-pick)
- [Three-Way Merge Explained](#three-way-merge-explained)
- [Common Scenarios](#common-scenarios)
- [Alternative: Patch Application](#alternative-patch-application)
- [Key Commands Reference](#key-commands-reference)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)

## Overview

Git cherry-pick allows you to apply specific commits from one branch to another. While it seems straightforward, conflicts can trigger complex three-way merges that bring in unexpected changes from the branch history.

## Understanding Cherry-Pick

### What `git cherry-pick` Does
- Applies a **specific commit** from one branch onto another
- Attempts to apply just that commit, not the entire branch history
- Falls back to a **three-way merge** when conflicts arise

### Basic Usage
```bash
git cherry-pick <commit-hash>
git cherry-pick <tag-name>
```

## Three-Way Merge Explained

When cherry-pick encounters conflicts, Git performs a three-way merge comparing:

| Component | Description |
|-----------|-------------|
| **Base (Common Ancestor)** | Where both branches diverged from |
| **Source (Cherry-Picked Commit)** | The changes you're trying to apply |
| **Target (Current HEAD)** | The branch you're applying the commit to |

### Why This Matters
The three-way merge can bring in changes that weren't explicitly in the cherry-picked commit because Git analyzes the entire difference between the source branch and the common ancestor.

## Common Scenarios

### Scenario 1: Clean Cherry-Pick (No Conflicts)
```bash
# Setup
git checkout master
git cherry-pick feature-branch-tag
```
**Result**: Commit applies cleanly without merge conflicts.

### Scenario 2: Cherry-Pick with Conflicts
```bash
# Setup with conflicting changes
git checkout master  
git cherry-pick feature-branch-tag
```
**Output**:
```
Auto-merging afile
CONFLICT (content): Merge conflict in afile
error: could not apply b253350... second change on feature1 added
```

**Conflict Resolution**:
1. Open the conflicted file and resolve manually
2. Look for conflict markers: `<<<<<<<`, `=======`, `>>>>>>>`
3. Edit to keep desired changes
4. Stage resolved files: `git add <filename>`
5. Continue: `git cherry-pick --continue`

### Scenario 3: Unexpected Lines in Conflicts
You may see lines in conflicts that weren't shown by `git show <commit>`. This happens because the three-way merge considers the entire branch evolution, not just the single commit.

## Alternative: Patch Application

When cherry-pick doesn't work as expected, you can manually apply changes using patches.

### Creating and Applying Patches
```bash
# Create patch from commit
git diff-tree -p <commit> > commit.patch

# Apply patch
cat commit.patch | git apply

# Apply with rejection handling
cat commit.patch | git apply --reject
```

### Handling Patch Rejections
```bash
# View rejected changes
cat <filename>.rej

# Manually edit files to apply rejected changes
nano <filename>

# Stage and commit manually
git add <filename>
git commit -m "Manually applied patch from <commit>"
```

## Key Commands Reference

### Cherry-Pick Commands
```bash
git cherry-pick <commit>        # Apply specific commit
git cherry-pick --continue      # Continue after resolving conflicts
git cherry-pick --abort         # Cancel cherry-pick operation
git cherry-pick --skip          # Skip current commit
```

### Patch Commands
```bash
git diff-tree -p <commit> > file.patch    # Create patch
cat file.patch | git apply                # Apply patch
cat file.patch | git apply --reject       # Apply with rejection
git apply --check file.patch              # Check if patch applies
```

### Debugging Commands
```bash
git status                                     # Check current repository state
git log --all --oneline --decorate --graph    # Visual history with all branches
git log --all --oneline --decorate --graph --patch  # Include patch content
git show <commit>                              # Detailed commit information
git branch                                     # List current branches
cat <filename>                                 # Check file contents
ls                                            # List files in directory
pwd                                           # Verify current location
```

## Troubleshooting

### Problem: Cherry-pick brings unexpected changes
**Cause**: Three-way merge includes changes from branch history
**Solution**: Use patch application for more precise control

### Problem: Patch fails to apply
**Cause**: Context lines have changed since patch creation
**Solutions**:
- Use `git apply --reject` and manually resolve
- Use `git apply --ignore-space-change` or `--ignore-whitespace`
- Apply patches against the correct base commit

### Problem: Lost track during conflict resolution
**Solutions**:
```bash
git status                    # Check current state
git cherry-pick --abort       # Start over
git log --oneline --graph     # Visualize history
```

## Best Practices

### When to Use Cherry-Pick
- ✅ Applying specific bug fixes across branches
- ✅ Backporting features to release branches
- ✅ Selective integration of changes

### When to Avoid Cherry-Pick
- ❌ Moving large feature sets (use merge or rebase instead)
- ❌ When branch histories are significantly diverged
- ❌ For commits with many dependencies on other commits

### Tips for Success
1. **Always check the commit first**: Use `git show <commit>` to understand what you're applying
2. **Understand your branch topology**: Use `git log --graph` to visualize relationships
3. **Have a backup plan**: Know how to abort operations
4. **Test in a separate branch**: Try cherry-picks in experimental branches first
5. **Document your process**: Keep notes of which commits were cherry-picked where
6. **Create multiple test repositories**: Don't be afraid to `rm -rf` and start over when experimenting
7. **Use tags for reference points**: Tag important commits like `git tag feature1tag` for easy reference
8. **Always verify clean state**: Use `git status` after aborting operations to ensure clean working directory

### Manual Resolution Strategy
When automatic tools fail:
1. Create patch files for precise control
2. Use `--reject` to salvage what can be applied automatically
3. Manually resolve rejected hunks
4. Stage and commit with descriptive messages
5. Verify results with `git show HEAD`

## Complete Practical Example

Here's a step-by-step walkthrough based on actual testing, showing the complete workflow from repository setup to final resolution:

### Initial Repository Setup
```bash
# Create and initialize repository
mkdir git_patch_apply
cd git_patch_apply/
git init

# Create initial file and commit
touch afile
git add afile
git commit -m 'afile added'

# Add first change on master
echo "first change on master" >> afile
git commit -am 'first change on master added'

# Create feature branch before adding more to master
git branch feature1

# Add second change on master (this will cause conflicts later)
echo "second change on master" >> afile
git commit -am 'second change on master added'
```

### Feature Branch Development
```bash
# Switch to feature branch
git checkout feature1

# Make changes on feature branch
echo "first change on feature1" >> afile
git commit -am 'first change on feature1 added'

# Add more changes including a new file
echo "second change on feature1" >> afile
echo "newfile on feature1" >> newfile
git add newfile
git commit -am 'second change on feature1 added'

# Tag the commit for easy reference
git tag feature1tag

# Return to master
git checkout master
```

### Visualizing the Branch Structure
```bash
# See the complete history with visual graph
git log --all --oneline --graph --decorate
```
**Output shows**:
```
* b253350 (tag: feature1tag, feature1) second change on feature1 added
* bde91f1 first change on feature1 added
| * 433b872 (HEAD -> master) second change on master added
|/
* eef77f1 first change on master added
* fa63573 afile added
```

### Cherry-Pick Attempt and Conflict
```bash
# Examine what we're about to cherry-pick
git show feature1tag

# Attempt cherry-pick
git cherry-pick feature1tag
```
**Result**: Conflict occurs because both branches modified the same file.

```bash
# Check conflict state
cat afile
# Shows conflict markers with unexpected content

# Abort the cherry-pick to try alternative approach
git cherry-pick --abort
git status  # Verify clean state
```

### Alternative: Patch Application Workflow
```bash
# Create patch from the specific commit
git diff-tree -p feature1tag > feature1tag.patch

# Examine the patch content
cat feature1tag.patch
```
**Patch content**:
```diff
diff --git a/afile b/afile
index abc123..def456 100644
--- a/afile
+++ b/afile
@@ -1,2 +1,3 @@
 first change on master
 first change on feature1
+second change on feature1
diff --git a/newfile b/newfile
new file mode 100644
index 0000000..789abc
--- /dev/null
+++ b/newfile
@@ -0,0 +1 @@
+newfile on feature1
```

### Handling Patch Application Failure
```bash
# Try normal patch application (this will fail)
cat feature1tag.patch | git apply
# Error: patch failed: afile:1 - patch does not apply

# Use rejection mode to salvage what we can
cat feature1tag.patch | git apply --reject

# Check results
git status    # Shows newfile added successfully
ls           # Shows afile.rej was created
```

### Manual Resolution Process
```bash
# Examine what was rejected
cat afile.rej
```
**Rejection content shows**:
```diff
***************
*** 1,2 ****
  first change on master
  first change on feature1
+ second change on feature1
--- 1,2 ----
  first change on master
  first change on feature1
+ second change on feature1
```

```bash
# Check current state of conflicted file
cat afile
# Shows: first change on master
#        second change on master

# Manually edit file to apply rejected changes
vi afile
# Add the missing lines:
# first change on master
# second change on master  
# first change on feature1
# second change on feature1

# Stage all changes and commit
git add afile newfile
git commit -m "Manually applied patch from feature1tag"
```

### Verification and Final State
```bash
# Verify the final result
git log --all --oneline --decorate --graph
```
**Final history**:
```
* ca19d34 (HEAD -> master) Manually applied patch from feature1tag
* 433b872 second change on master added
| * b253350 (tag: feature1tag, feature1) second change on feature1 added
| * bde91f1 first change on feature1 added
|/
* eef77f1 first change on master added
* fa63573 afile added
```

```bash
# Examine the final commit content
git show HEAD
# Shows both the file modifications and new file addition
```

### Key Observations from This Workflow

1. **Cherry-pick conflicts are complex**: The conflict included lines not directly modified by the target commit due to three-way merge behavior.

2. **Patch application provides more control**: Even when `git apply` fails, `--reject` mode allows partial application and shows exactly what failed.

3. **Manual resolution is often necessary**: Complex scenarios require understanding the intent and manually integrating changes.

4. **Multiple experimental attempts**: The command history shows several repository recreations to test different scenarios - this is a good practice for understanding Git behavior.

5. **Verification is crucial**: Always use `git show`, `git log --graph`, and file inspection to verify results match expectations.

---

**Remember**: Git cherry-pick is powerful but can be unpredictable with conflicts. Understanding three-way merges and having patch application skills in your toolkit will make you more effective at managing complex Git scenarios.
