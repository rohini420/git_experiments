# 🚀 Git Experiment: Pushing a New Branch Without an Upstream

This experiment walks through what happens when you try to push a newly created local branch to a remote repository **without setting its upstream tracking**. You'll see the Git error, fix it, and understand what's happening behind the scenes.

---

## 📁 Folder Structure

branch-upstream-push/
├── git_origin/ # Simulated remote repository (non-bare)
├── cloned_repo/ # Local clone acting as a contributor
├── command_log.txt # Full Git command history used in this experiment
└── README.md # This file explaining the experiment


---

## 🧪 Experiment Summary

We simulate a real-world situation where:

1. A remote repo (`git_origin`) is created and initialized.
2. A clone (`cloned_repo`) is made to act as a developer's working copy.
3. Inside `cloned_repo`, a new branch called `feature1` is created.
4. A change is committed to `feature1`.
5. On running `git push`, Git throws an error because `feature1` has no upstream reference.
6. The issue is resolved using:
   ```bash
   git push --set-upstream origin feature1

🧠 Key Learning
When you create a new branch locally and attempt to push it to a remote for the first time, Git doesn’t automatically know which remote branch to track unless you specify it. That’s why this error appears:

❌ "The current branch 'feature1' has no upstream branch."

You can fix it in two ways:

By specifying the branch and remote directly:

git push -u origin feature1

Or by setting the upstream explicitly later:

git branch --set-upstream-to=origin/feature1 feature1

After the upstream is set, all future git push and git pull commands will automatically sync with the correct remote branch.

✅ Result

After setting the upstream, the branch successfully tracks the remote branch:

git branch -vv

* feature1 a86a8a2 [origin/feature1] second commit
  master    6447cd3 [origin/master] first commit

🧾 Bonus Tip
You can use the -u flag just once while pushing the branch the first time. From then on, just git push and git pull will work smoothly.
