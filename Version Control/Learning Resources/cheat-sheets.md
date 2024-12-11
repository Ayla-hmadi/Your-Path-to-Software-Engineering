# Version Control Cheat Sheets

## Git Quick Reference

### Basic Commands
```bash
# Repository Setup
git init                    # Initialize repository
git clone <url>            # Clone repository
git remote add <name> <url> # Add remote

# Daily Operations
git status                 # Check status
git add <file>            # Stage file
git add .                 # Stage all changes
git commit -m "message"   # Commit changes
git push origin <branch>  # Push changes
git pull origin <branch>  # Pull changes

# Branch Operations
git branch                # List branches
git branch <name>        # Create branch
git checkout <branch>    # Switch branches
git merge <branch>       # Merge branches
```

### Advanced Operations
```bash
# History
git log                  # View history
git log --graph         # Graph view
git blame <file>        # File history
git diff                # View changes

# Undoing Changes
git reset HEAD~1        # Undo last commit
git reset --hard HEAD   # Discard changes
git revert <commit>     # Revert commit
git checkout -- <file>  # Discard file changes

# Stashing
git stash              # Stash changes
git stash pop          # Apply stash
git stash list         # List stashes
git stash drop         # Delete stash
```

## SVN Quick Reference

### Basic Operations
```bash
# Repository Operations
svn checkout <url>      # Check out repository
svn update             # Update working copy
svn commit -m "msg"    # Commit changes
svn add <path>         # Add files
svn delete <path>      # Delete files

# Information
svn status            # Check status
svn log              # View history
svn diff             # View changes
svn info             # Repository info
```

### Branch Management
```bash
# Branch Operations
svn copy <src> <dst>   # Create branch
svn merge <src>        # Merge changes
svn switch <url>       # Switch branches
svn resolve            # Resolve conflicts
```

## Mercurial Quick Reference

### Basic Commands
```bash
# Repository Setup
hg init               # Initialize repository
hg clone <url>        # Clone repository
hg pull              # Pull changes
hg push              # Push changes

# Daily Operations
hg status            # Check status
hg add               # Add files
hg commit -m "msg"   # Commit changes
hg update            # Update working dir
```

### Advanced Operations
```bash
# Branches
hg branch <name>     # Create branch
hg branches          # List branches
hg merge <branch>    # Merge branches
hg update <branch>   # Switch branches

# History
hg log              # View history
hg annotate <file>  # File history
hg diff            # View changes
hg backout         # Reverse changes
```

## Common Workflows

### Feature Branch Workflow
```bash
# Start Feature
git checkout -b feature/name main
# Make changes
git add .
git commit -m "feature changes"
# Update from main
git fetch origin
git rebase origin/main
# Complete Feature
git checkout main
git merge feature/name
```

### Release Process
```bash
# Create Release
git checkout -b release/v1.0 develop
# Stabilize and test
git commit -m "version bump"
# Finish Release
git checkout main
git merge release/v1.0
git tag -a v1.0.0 -m "Release v1.0.0"
```

## Configuration Templates

### Git Config
```ini
[user]
    name = Your Name
    email = your.email@example.com

[core]
    editor = vim
    whitespace = trailing-space,space-before-tab

[alias]
    st = status
    ci = commit
    co = checkout
    br = branch
```

### Git Ignore
```gitignore
# Common patterns
*.log
*.tmp
.DS_Store
node_modules/
build/
dist/

# IDE files
.idea/
.vscode/
*.swp
```

## Quick Solutions

### Common Problems
```bash
# Fix Last Commit
git commit --amend

# Undo Merge
git reset --hard HEAD~1

# Clean Working Directory
git clean -fd

# Remove Untracked Files
git clean -n  # Dry run
git clean -f  # Remove files
```

### Conflict Resolution
```bash
# During Merge Conflict
git status                  # Check status
git diff                    # View conflicts
git checkout --ours file   # Keep our version
git checkout --theirs file # Keep their version
git add file              # Mark as resolved
```

## Security Best Practices

### Authentication
```bash
# Generate SSH Key
ssh-keygen -t ed25519 -C "email"

# Add SSH Key
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Configure GPG
git config --global commit.gpgsign true
git config --global user.signingkey <key-id>
```

### Access Control
```bash
# File Permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519

# Repository Permissions
git config core.sharedRepository group
```

## Integration Examples

### CI/CD Pipeline
```yaml
# GitHub Actions
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm test
```

### Hook Scripts
```bash
# Pre-commit hook
#!/bin/sh
npm test
npm run lint

# Pre-push hook
#!/bin/sh
npm run build
npm run test:e2e
```
