# Git Flow Workflow Guide

## Overview
Git Flow is a branching model that provides a robust framework for managing larger projects. It defines specific branch roles and how they should interact, creating a structured release process.

## Core Branches

### Main Branch
```bash
# Creation
git checkout -b main develop
git push -u origin main

# Protection
- Requires pull request
- Requires code review
- Requires passing tests
```

### Develop Branch
```bash
# Creation
git checkout -b develop main
git push -u origin develop

# Usage
- Integration branch
- Latest delivered development changes
- Nightly builds
```

## Supporting Branches

### Feature Branches
```bash
# Starting Feature
git checkout -b feature/user-auth develop
git push -u origin feature/user-auth

# Development
git add .
git commit -m "Add user authentication"

# Finishing Feature
git checkout develop
git merge --no-ff feature/user-auth
git push origin develop
git branch -d feature/user-auth
```

### Release Branches
```bash
# Creating Release
git checkout -b release/1.2.0 develop
git push -u origin release/1.2.0

# Stabilization
git add .
git commit -m "Bump version to 1.2.0"

# Finishing Release
git checkout main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "Version 1.2.0"
git checkout develop
git merge --no-ff release/1.2.0
git branch -d release/1.2.0
```

### Hotfix Branches
```bash
# Creating Hotfix
git checkout -b hotfix/1.2.1 main
git push -u origin hotfix/1.2.1

# Fix Implementation
git add .
git commit -m "Fix critical bug"

# Finishing Hotfix
git checkout main
git merge --no-ff hotfix/1.2.1
git tag -a v1.2.1 -m "Version 1.2.1"
git checkout develop
git merge --no-ff hotfix/1.2.1
git branch -d hotfix/1.2.1
```

## Workflow Procedures

### Feature Development
1. **Start Feature**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/new-feature
   ```

2. **Development Process**
   ```bash
   # Regular commits
   git add .
   git commit -m "Feature progress"
   
   # Push changes
   git push origin feature/new-feature
   ```

3. **Complete Feature**
   ```bash
   git checkout develop
   git pull origin develop
   git merge --no-ff feature/new-feature
   git push origin develop
   ```

### Release Process
1. **Create Release**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b release/1.2.0
   ```

2. **Stabilization**
   ```bash
   # Version bump
   # Update CHANGELOG
   # Fix release issues
   git commit -m "Prepare release 1.2.0"
   ```

3. **Finalize Release**
   ```bash
   git checkout main
   git merge --no-ff release/1.2.0
   git tag -a v1.2.0
   git checkout develop
   git merge --no-ff release/1.2.0
   ```

### Hotfix Process
1. **Start Hotfix**
   ```bash
   git checkout main
   git checkout -b hotfix/1.2.1
   ```

2. **Implement Fix**
   ```bash
   # Fix critical issue
   git commit -m "Fix critical issue"
   ```

3. **Complete Hotfix**
   ```bash
   git checkout main
   git merge --no-ff hotfix/1.2.1
   git tag -a v1.2.1
   git checkout develop
   git merge --no-ff hotfix/1.2.1
   ```

## Best Practices

### Branch Naming
```
main           - Production code
develop        - Development code
feature/*      - New features
release/*      - Release preparation
hotfix/*       - Emergency fixes
```

### Commit Messages
```
# Feature commits
feat: Add new functionality

# Release commits
chore: Bump version to 1.2.0

# Hotfix commits
fix: Address critical issue
```

### Tag Management
```bash
# Create annotated tag
git tag -a v1.2.0 -m "Release version 1.2.0"

# Push tags
git push origin v1.2.0
```

## Integration

### Continuous Integration
1. **Build Automation**
   ```yaml
   # Feature branches
   - Build
   - Unit tests
   - Linting
   
   # Release branches
   - Integration tests
   - Performance tests
   - Security scans
   ```

2. **Deployment Pipeline**
   ```
   develop  → staging
   release  → pre-production
   main     → production
   ```

### Quality Control
1. **Code Review**
   ```
   - Feature completion
   - Release readiness
   - Hotfix verification
   ```

2. **Testing Requirements**
   ```
   Features:
   - Unit tests
   - Integration tests
   
   Releases:
   - Full test suite
   - Performance tests
   
   Hotfixes:
   - Regression tests
   - Security tests
   ```

## Troubleshooting

### Common Issues
1. **Merge Conflicts**
   ```bash
   # Update branch
   git fetch origin
   git rebase origin/develop
   
   # Resolve conflicts
   git add .
   git rebase --continue
   ```

2. **Release Issues**
   ```bash
   # Cherry-pick fixes
   git cherry-pick <commit>
   
   # Update version
   git commit --amend
   ```

### Recovery Procedures
1. **Reverting Changes**
   ```bash
   # Revert merge
   git revert -m 1 <merge-commit>
   
   # Revert release
   git tag -d v1.2.0
   git push origin :refs/tags/v1.2.0
   ```

2. **Branch Recovery**
   ```bash
   # Recover deleted branch
   git checkout -b recover-branch <last-commit>
   
   # Reset to stable state
   git reset --hard <stable-commit>
   ```
