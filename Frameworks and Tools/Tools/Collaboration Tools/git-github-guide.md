# Git and GitHub Guide

## Introduction
Git is a distributed version control system that enables teams to track changes, collaborate on code, and maintain project history. GitHub is a web-based platform that provides hosting for Git repositories along with additional collaboration features.

## Git Setup and Configuration

### Initial Setup
```bash
# Set your username and email
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Configure line ending preferences
git config --global core.autocrlf true  # Windows
git config --global core.autocrlf input # Mac/Linux
```

### SSH Key Setup for GitHub
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Start ssh-agent
eval "$(ssh-agent -s)"

# Add SSH key to ssh-agent
ssh-add ~/.ssh/id_ed25519
```

## Basic Git Commands

### Repository Operations
```bash
# Initialize new repository
git init

# Clone existing repository
git clone <repository-url>

# Add remote repository
git remote add origin <repository-url>

# View remote repositories
git remote -v
```

### Basic Workflow Commands
```bash
# Check status
git status

# Add files to staging
git add <file>          # Add specific file
git add .               # Add all files

# Commit changes
git commit -m "commit message"

# Push changes
git push origin <branch-name>

# Pull changes
git pull origin <branch-name>
```

## Branching and Merging

### Branch Management
```bash
# Create new branch
git branch <branch-name>

# Switch to branch
git checkout <branch-name>
# Or using newer syntax
git switch <branch-name>

# Create and switch to new branch
git checkout -b <branch-name>
# Or using newer syntax
git switch -c <branch-name>

# List branches
git branch            # Local branches
git branch -r         # Remote branches
git branch -a         # All branches

# Delete branch
git branch -d <branch-name>     # Safe delete
git branch -D <branch-name>     # Force delete
```

### Merging
```bash
# Merge branch into current branch
git merge <branch-name>

# Abort merge in case of conflicts
git merge --abort

# Continue merge after resolving conflicts
git merge --continue
```

## Advanced Git Operations

### Stashing Changes
```bash
# Stash changes
git stash save "stash message"

# List stashes
git stash list

# Apply stash
git stash apply stash@{0}

# Pop stash
git stash pop

# Drop stash
git stash drop stash@{0}
```

### History and Logs
```bash
# View commit history
git log
git log --oneline
git log --graph --oneline --decorate

# View file history
git log -p <file>

# View changes
git diff                    # Working directory vs staging
git diff --staged          # Staging vs last commit
git diff <commit1> <commit2> # Between commits
```

## GitHub Features

### Pull Requests
1. **Creating Pull Requests**
   - Fork repository (if necessary)
   - Create feature branch
   - Make changes and commit
   - Push to GitHub
   - Open pull request

2. **Review Process**
   - Code review comments
   - Suggestion feature
   - Review approval/rejection
   - Continuous Integration checks

### Issues
- Create detailed issue descriptions
- Use labels and milestones
- Link issues to pull requests
- Close issues through commits

### GitHub Actions
```yaml
# Basic GitHub Action workflow
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Run tests
      run: |
        npm install
        npm test
```

## Best Practices

### Commit Messages
1. **Structure**
   ```
   <type>(<scope>): <subject>

   <body>

   <footer>
   ```

2. **Types**
   - feat: New feature
   - fix: Bug fix
   - docs: Documentation
   - style: Formatting
   - refactor: Code restructuring
   - test: Adding tests
   - chore: Maintenance

### Branching Strategy
1. **Git Flow**
   - main/master: Production code
   - develop: Development code
   - feature/*: New features
   - release/*: Release preparation
   - hotfix/*: Production fixes

2. **Trunk-Based Development**
   - main: Primary branch
   - feature/*: Short-lived feature branches

### Code Review Guidelines
1. **Review Checklist**
   - Code functionality
   - Test coverage
   - Documentation
   - Performance implications
   - Security considerations

2. **Review Etiquette**
   - Be respectful and constructive
   - Explain reasoning
   - Suggest improvements
   - Acknowledge good practices

## Troubleshooting

### Common Issues
1. **Merge Conflicts**
   ```bash
   # Identify conflicting files
   git status

   # Resolve conflicts in files
   # Choose changes to keep

   # Mark as resolved
   git add <resolved-files>

   # Complete merge
   git commit
   ```

2. **Undoing Changes**
   ```bash
   # Discard working directory changes
   git restore <file>

   # Unstage changes
   git restore --staged <file>

   # Revert commit
   git revert <commit>

   # Reset to specific commit
   git reset --hard <commit>
   ```

## Additional Resources
- [Git Documentation](https://git-scm.com/doc)
- [GitHub Guides](https://guides.github.com/)
- [GitHub Skills](https://skills.github.com/)
- [Pro Git Book](https://git-scm.com/book/en/v2)
