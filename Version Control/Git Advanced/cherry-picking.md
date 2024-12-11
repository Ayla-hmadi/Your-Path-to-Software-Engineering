# Git Cherry Picking Guide

## Overview
Cherry picking in Git allows you to copy specific commits from one branch to another. This selective commit application is useful for applying specific changes without merging entire branches.

## Basic Cherry Picking

### Single Commit
```bash
# Basic cherry-pick
git cherry-pick <commit-hash>

# Cherry-pick without committing
git cherry-pick -n <commit-hash>

# Cherry-pick and edit commit message
git cherry-pick -e <commit-hash>
```

### Multiple Commits
```bash
# Cherry-pick range of commits
git cherry-pick <start-commit>..<end-commit>

# Cherry-pick multiple specific commits
git cherry-pick <commit1> <commit2> <commit3>

# Cherry-pick range including first commit
git cherry-pick <start-commit>^..<end-commit>
```

## Advanced Operations

### Cherry-Pick Options
```bash
# Keep original author
git cherry-pick -x <commit-hash>

# Add "cherry picked from" message
git cherry-pick -x <commit-hash>

# Allow empty commits
git cherry-pick --allow-empty <commit-hash>

# Record original commit in message
git cherry-pick --record-origin <commit-hash>
```

### Conflict Resolution
```bash
# When conflicts occur
git status                  # Check conflict status
git diff                    # Review conflicts

# Resolution options
git cherry-pick --continue  # After resolving conflicts
git cherry-pick --abort     # Cancel operation
git cherry-pick --quit      # Exit but keep changes
```

## Use Cases

### Feature Backporting
```bash
# Backport to maintenance branch
git checkout maintenance-branch
git cherry-pick <feature-commit>

# Backport multiple fixes
git checkout stable
git cherry-pick <fix-1> <fix-2> <fix-3>
```

### Selective Feature Transfer
```bash
# Transfer specific features
git checkout target-branch
git cherry-pick <feature-commits>

# Transfer with dependencies
git cherry-pick <dependency-commit> <feature-commit>
```

## Best Practices

### Before Cherry Picking
1. **Preparation**
   ```bash
   # Identify correct commits
   git log --oneline --graph
   
   # Create backup branch
   git checkout -b backup-branch
   ```

2. **Validation**
   - Review commit contents
   - Check dependencies
   - Verify build/tests
   - Document changes

### During Cherry Pick
1. **Commit Selection**
   - Choose atomic commits
   - Consider dependencies
   - Note related changes
   - Track applied commits

2. **Conflict Handling**
   ```bash
   # Review changes carefully
   git diff
   
   # Use merge tools
   git mergetool
   
   # Validate after resolution
   git status
   ```

## Advanced Techniques

### Commit Manipulation
```bash
# Cherry-pick and squash
git cherry-pick --no-commit <commit1> <commit2>
git commit -m "Combined changes"

# Cherry-pick specific changes
git cherry-pick -n <commit>
git reset
git add -p
git commit
```

### Automated Cherry Picking
```bash
# Script for multiple commits
for commit in $(git rev-list <start>..<end>)
do
    git cherry-pick -x $commit || break
done
```

## Common Issues

### Dependency Problems
1. **Missing Dependencies**
   ```bash
   # Check commit dependencies
   git log --graph --oneline
   
   # Include dependent commits
   git cherry-pick <dependency> <target>
   ```

2. **Resolution Steps**
   - Identify dependencies
   - Order commits correctly
   - Track applied changes
   - Verify functionality

### Conflict Management
1. **Complex Conflicts**
   ```bash
   # Create temporary branch
   git checkout -b cherry-pick-temp
   
   # Apply changes manually
   git diff <commit> | git apply --3way
   ```

2. **Recovery Options**
   ```bash
   # Abort and restart
   git cherry-pick --abort
   
   # Continue after fixes
   git cherry-pick --continue
   ```

## Integration Patterns

### Release Management
1. **Hotfix Application**
   ```bash
   # Apply hotfix to multiple versions
   git checkout release-1.0
   git cherry-pick <hotfix-commit>
   
   git checkout release-1.1
   git cherry-pick <hotfix-commit>
   ```

2. **Feature Backporting**
   ```bash
   # Backport to maintenance versions
   git checkout maintenance
   git cherry-pick -x <feature-commit>
   ```

### Workflow Integration
1. **Code Review Process**
   ```bash
   # Create review branch
   git checkout -b review-branch
   git cherry-pick <commit-to-review>
   ```

2. **CI/CD Pipeline**
   ```bash
   # Test changes separately
   git checkout test-branch
   git cherry-pick <commit>
   # Run tests
   ```

## Documentation

### Tracking Changes
1. **Commit Messages**
   ```bash
   # Add cherry-pick information
   git cherry-pick -x <commit>
   
   # Custom message
   git cherry-pick -e <commit>
   ```

2. **Change Log**
   - Record cherry-picked commits
   - Note source branches
   - Document conflicts
   - Track dependencies

## Best Practices Summary

### General Guidelines
1. **Clean Commits**
   - Single responsibility
   - Clear messages
   - Complete changes
   - Proper testing

2. **Documentation**
   - Track sources
   - Note dependencies
   - Record conflicts
   - Update changelogs

### Safety Measures
1. **Before Cherry-Pick**
   - Clean working directory
   - Create backup branch
   - Verify commits
   - Check dependencies

2. **After Cherry-Pick**
   - Verify changes
   - Run tests
   - Update documentation
   - Clean up branches
