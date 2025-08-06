# Git Hooks Tutorial

A comprehensive guide to understanding and implementing Git hooks for automated workflow enforcement.

## Overview

Git hooks are scripts that run automatically at certain points in the Git workflow. They allow you to enforce coding standards, validate commit messages, and implement custom business rules before code changes are accepted.

## Project Structure

```
git_hooks/
├── git_origin/     # Bare repository (simulates remote server)
└── git_clone/      # Working repository (your local workspace)
```

This setup simulates a typical client-server Git workflow where:
- `git_origin` acts as the central repository (like GitHub)
- `git_clone` is where developers work locally

## Setup Instructions

### 1. Initialize the Environment

```bash
# Create project structure
mkdir git_hooks
cd git_hooks

# Create bare repository (server)
mkdir git_origin
cd git_origin
git init --bare
cd ..

# Clone to working directory (client)
git clone git_origin git_clone
cd git_clone

# Make initial commit
echo "Hello World" > file1
git add file1
git commit -m "Initial commit"
git push origin master
```

## Types of Git Hooks

### Client-Side Hooks (Local Repository)

These hooks run on your local machine and affect only your workflow.

#### Pre-Commit Hook

**Location:** `.git/hooks/pre-commit`

Runs before a commit is created. Perfect for code quality checks.

**Example 1: Weekend Work Prevention**
```bash
#!/bin/bash
echo "NO WORKING AT WEEKENDS!"
exit 1
```

**Example 2: Content Filtering**
```bash
#!/bin/bash
# Block commits containing political content
if grep -rni "politic" *; then
    echo "no politics allowed"
    exit 1
else
    echo "OK"
    exit 0
fi
```

**Setup:**
```bash
# Create the hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
if grep -rni "politic" *; then
    echo "no politics allowed"
    exit 1
else
    echo "OK"
    exit 0
fi
EOF

# Make executable
chmod +x .git/hooks/pre-commit
```

### Server-Side Hooks (Remote Repository)

These hooks run on the central repository and enforce team-wide policies.

#### Pre-Receive Hook

**Location:** `git_origin/hooks/pre-receive`

Runs when receiving a push, before any references are updated.

**Example: Enforce Ticket ID in Commit Messages**
```bash
#!/bin/bash
read _oldrev newrev _branch
git cat-file -p $newrev | grep '[A-Z][A-Z]*-[0-9][0-9]*'
```

This hook ensures all commit messages contain a ticket ID pattern like `BUG-123` or `TASK-456`.

**Setup:**
```bash
cd git_origin/hooks
cat > pre-receive << 'EOF'
#!/bin/bash
read _oldrev newrev _branch
git cat-file -p $newrev | grep '[A-Z][A-Z]*-[0-9][0-9]*'
EOF
chmod +x pre-receive
```

## Common Hook Scripts

### Available Hook Types

| Hook | Trigger | Use Case |
|------|---------|----------|
| `pre-commit` | Before commit creation | Code formatting, linting, tests |
| `commit-msg` | After commit message entry | Message format validation |
| `pre-push` | Before pushing to remote | Final checks before sharing |
| `pre-receive` | Before accepting push | Server-side validation |
| `post-update` | After successful push | Deployment, notifications |

## Testing Your Hooks

### Testing Pre-Commit Hook

```bash
# This should be blocked by political content filter
echo 'a political commit' >> file1
git commit -m "political comment"
# Output: no politics allowed

# This should succeed
echo 'boring comment' >> file1
git commit -am "boring comment"
# Output: OK
```

### Testing Pre-Receive Hook

```bash
# This should be rejected
git commit -am "no mention of ticket id"
git push
# Output: ! [remote rejected] master -> master (pre-receive hook declined)

# This should succeed
git commit --amend -m "BUG-123: fixing the error message"
git push
# Push accepted
```

## Important Notes

### Hook Limitations

⚠️ **Client-side hooks are NOT shared with the repository**
- Hooks live in `.git/hooks/` which is not tracked by Git
- Each developer must install hooks manually
- Not suitable for enforcing team-wide policies

✅ **Server-side hooks enforce team policies**
- Run on the central repository
- Automatically affect all team members
- Perfect for mandatory standards

### Best Practices

1. **Keep hooks fast** - Slow hooks frustrate developers
2. **Provide clear error messages** - Help developers understand what went wrong
3. **Use exit codes properly** - `exit 0` for success, `exit 1` for failure
4. **Test thoroughly** - Bad hooks can break workflows
5. **Document requirements** - Team members need to understand the rules

## Troubleshooting

### Hook Not Running
- Check if file is executable: `chmod +x .git/hooks/hookname`
- Ensure no `.sample` extension
- Verify script syntax

### Push Rejected
- Check commit message format
- Review server-side hook requirements
- Use `git commit --amend` to fix messages

### Bypassing Hooks (Emergency)
```bash
# Skip pre-commit hooks (use sparingly)
git commit --no-verify -m "Emergency fix"
```

## Advanced Examples

### Multi-Pattern Commit Message Validation
```bash
#!/bin/bash
read _oldrev newrev _branch
commit_msg=$(git cat-file -p $newrev | sed -n '5p')

# Check for ticket ID (JIRA format)
if echo "$commit_msg" | grep -q '[A-Z][A-Z]*-[0-9][0-9]*'; then
    exit 0
fi

# Check for conventional commits format
if echo "$commit_msg" | grep -qE '^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+'; then
    exit 0
fi

echo "Commit message must contain a ticket ID (e.g., PROJ-123) or follow conventional commits format"
exit 1
```

## Conclusion

Git hooks are powerful tools for maintaining code quality and enforcing project standards. Use client-side hooks for personal workflow improvements and server-side hooks for team-wide policy enforcement.

Remember: Good hooks help teams work better together by catching issues early and maintaining consistency across the codebase.