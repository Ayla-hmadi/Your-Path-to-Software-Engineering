# Git Branches and Merging Guide

## Branch Fundamentals

### Understanding Branches
- Lightweight movable pointers to commits
- Independent lines of development
- Parallel work streams
- Feature isolation

### Branch Types
```
1. Main/Master Branch
   - Primary production code
   - Stable releases
   - Long-term branch

2. Development Branch
   - Integration branch
   - Pre-production code
   - Feature collection

3. Feature Branches
   - New functionality
   - Bug fixes
   - Experimental features

4. Release Branches
   - Version preparation
   - Last-minute fixes
   - Documentation updates

5. Hotfix Branches
   - Emergency fixes
   - Production issues
   - Critical updates
```

## Branch Operations

### Basic Commands
```bash
# List branches
git branch                 # Local branches
git branch -r             # Remote branches
git branch -a             # All branches
git branch -v             # Verbose listing

# Create branch
git branch <branch-name>
git checkout -b <branch-name>
git switch -c <branch-name>   # New syntax

# Switch branches
git checkout <branch-name>
git switch <branch-name>      # New syntax

# Delete branch
git branch -d <branch-name>   # Safe delete
git branch -D <branch-name>   # Force delete
```

### Remote Branch Operations
```bash
# Push new branch
git push -u origin <branch-name>

# Track remote branch
git checkout --track origin/<branch-name>

# Delete remote branch
git push origin --delete <branch-name>

# Clean up deleted remote branches
git remote prune origin
git fetch --prune
```

## Merging Strategies

### Fast-Forward Merge
```bash
# Simple linear merge
git checkout main
git merge feature
```

### Three-Way Merge
```bash
# Create merge commit
git checkout main
git merge --no-ff feature
```

### Squash Merge
```bash
# Combine all commits
git checkout main
git merge --squash feature
git commit -m "Feature merged"
```

### Rebase
```bash
# Reapply commits on top
git checkout feature
git rebase main

# Interactive rebase
git rebase -i main
```

## Handling Merge Conflicts

### Conflict Resolution Process
1. **Identify Conflicts**
   ```bash
   git status           # Show conflicting files
   git diff             # Show conflict details
   ```

2. **Resolve Conflicts**
   ```
   <<<<<<< HEAD
   Current changes
   =======
   Incoming changes
   >>>>>>> feature
   ```

3. **Complete Merge**
   ```bash
   git add <resolved-files>
   git commit           # Complete merge
   ```

### Conflict Prevention
```bash
# Update branch before merging
git checkout feature
git fetch origin
git rebase origin/main

# Check for potential conflicts
git merge --no-commit --no-ff feature
```

## Branch Management Strategies

### Git Flow
```
Structure:
- main
  └── develop
      ├── feature/*
      ├── release/*
      └── hotfix/*

Workflow:
1. Create feature branch from develop
2. Develop feature
3. Merge back to develop
4. Create release branch
5. Merge to main and develop
```

### Trunk-Based Development
```
Structure:
- main
  └── feature/*

Workflow:
1. Small feature branches
2. Quick merges to main
3. Frequent integration
4. Feature flags
```

### GitHub Flow
```
Structure:
- main
  └── feature/*

Workflow:
1. Branch from main
2. Add commits
3. Open pull request
4. Review and discuss
5. Deploy and test
6. Merge to main
```

## Advanced Merging Techniques

### Cherry-Picking
```bash
# Apply specific commits
git cherry-pick <commit-hash>

# Cherry-pick range
git cherry-pick <start-commit>..<end-commit>

# Interactive cherry-pick
git cherry-pick -i <commit-hash>
```

### Merge Options
```bash
# Keep specific version
git merge -X ours feature    # Keep current branch version
git merge -X theirs feature  # Keep merged branch version

# Merge specific files
git checkout --patch feature file.txt
```

## Best Practices

### Branch Naming Conventions
```
feature/   - New features
bugfix/    - Bug fixes
hotfix/    - Urgent fixes
release/   - Release preparation
docs/      - Documentation
test/      - Testing branches
```

### Branch Lifecycle
1. **Creation**
   - Clear purpose
   - Descriptive name
   - Based on latest main

2. **Development**
   - Regular commits
   - Focused changes
   - Keep updated

3. **Completion**
   - Code review
   - Testing
   - Clean merge
   - Branch cleanup

### Review Process
1. **Pre-Merge Checklist**
   - Code complete
   - Tests passing
   - Documentation updated
   - Conflicts resolved
   - Style guidelines met

2. **Review Guidelines**
   - Clear description
   - Focused changes
   - Test coverage
   - Performance impact
   - Security considerations

## Troubleshooting

### Common Issues
```bash
# Undo last merge
git reset --hard HEAD~1

# Abort current merge
git merge --abort

# Fix wrong branch base
git rebase --onto new-base old-base feature

# Recover deleted branch
git reflog
git checkout -b recovered-branch <hash>
```

### Best Practices for Recovery
1. **Before Merging**
   - Create backup branch
   - Review changes
   - Test integration
   - Update documentation

2. **After Issues**
   - Document problem
   - Create recovery plan
   - Test solution
   - Update procedures
