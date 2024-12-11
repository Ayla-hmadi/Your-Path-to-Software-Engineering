# Git Stashing Guide

## Overview
Git stash is a powerful feature that temporarily shelves (or stashes) changes you've made to your working copy so you can work on something else, and then come back and re-apply them later.

## Basic Stashing Operations

### Creating Stashes
```bash
# Basic stash
git stash

# Stash with message
git stash save "Work in progress on feature X"

# Stash including untracked files
git stash -u

# Stash including ignored files
git stash -a

# Interactive stashing
git stash -p
```

### Managing Stashes
```bash
# List all stashes
git stash list

# Show stash contents
git stash show
git stash show -p  # Detailed patch view

# Show specific stash
git stash show stash@{n}
```

### Applying Stashes
```bash
# Apply most recent stash
git stash apply

# Apply specific stash
git stash apply stash@{n}

# Apply and remove stash
git stash pop

# Apply specific stash and remove it
git stash pop stash@{n}
```

## Advanced Stashing

### Branch Operations
```bash
# Create branch from stash
git stash branch new-branch

# Create branch from specific stash
git stash branch new-branch stash@{n}

# Apply stash to new branch
git checkout -b new-branch
git stash apply
```

### Cleaning Up Stashes
```bash
# Remove single stash
git stash drop
git stash drop stash@{n}

# Remove all stashes
git stash clear

# Clean specific stashes
git stash drop stash@{2}
git stash drop stash@{1}
```

## Stashing Patterns

### Partial Stashing
```bash
# Interactive stash selection
git stash -p

# Stash specific files
git stash push -m "message" file1.txt file2.txt

# Stash with pattern
git stash push -m "message" "*.txt"
```

### Stash Management
1. **Naming Conventions**
   ```bash
   git stash save "feature/ABC-123: Work in progress"
   git stash save "bugfix/XYZ-789: Temporary fix"
   ```

2. **Organization**
   ```bash
   # Feature work
   git stash save "feature: Description"
   
   # Bug fixes
   git stash save "bugfix: Description"
   
   # Experimental changes
   git stash save "experiment: Description"
   ```

## Common Use Cases

### Context Switching
```bash
# Save current work
git stash save "Working on feature A"

# Switch context
git checkout other-branch

# Return to work
git checkout original-branch
git stash pop
```

### Clean Working Directory
```bash
# Quick save before pull
git stash
git pull
git stash pop

# Clean for checkout
git stash
git checkout other-branch
```

### Experimental Changes
```bash
# Save experimental work
git stash save "Experimental: New approach"

# Try different approach
# If new approach fails
git stash apply stash@{0}
```

## Conflict Resolution

### Handling Stash Conflicts
```bash
# When conflicts occur during pop/apply
git status                  # Check conflict status
git diff                    # Review conflicts
git merge-tool              # Resolve conflicts
git add <resolved-files>    # Mark as resolved
git reset --hard           # Reset if needed
```

### Recovery Strategies
```bash
# If stash apply fails
git reset --hard HEAD      # Clean working directory
git stash apply            # Try again

# Create branch for resolution
git stash branch temp-branch stash@{0}
```

## Best Practices

### Stash Management
1. **Regular Cleanup**
   - Review stash list regularly
   - Drop resolved stashes
   - Document long-term stashes
   - Limit stash count

2. **Clear Documentation**
   - Descriptive messages
   - Include ticket numbers
   - Note dependencies
   - Mark experimental work

### Safety Measures
1. **Before Stashing**
   - Review changes
   - Test compilation
   - Clean working directory
   - Note dependencies

2. **After Applying**
   - Verify changes
   - Test functionality
   - Resolve conflicts
   - Update dependencies

## Troubleshooting

### Common Issues
1. **Lost Stashes**
   ```bash
   # Check reflog
   git fsck --unreachable
   git log --graph --oneline $(git fsck --unreachable | grep commit | cut -d' ' -f3)
   ```

2. **Conflict Recovery**
   ```bash
   # Abort stash application
   git reset --hard HEAD
   
   # Try alternative approach
   git stash branch recovery-branch
   ```

### Prevention Strategies
1. **Regular Practices**
   - Use descriptive messages
   - Limit stash lifetime
   - Regular cleanup
   - Document complex stashes

2. **Backup Approaches**
   ```bash
   # Create backup branch
   git checkout -b backup-branch
   git stash apply
   git checkout previous-branch
   ```

## Integration with Workflows

### Development Process
1. **Feature Development**
   ```bash
   git stash save "feature/ABC-123: WIP"
   git checkout -b feature/ABC-123
   git stash apply
   ```

2. **Code Review**
   ```bash
   git stash save "review-changes"
   git checkout -b review-branch
   git stash pop
   ```

### CI/CD Integration
1. **Pre-commit Hooks**
   ```bash
   # Automatic stashing in hooks
   if [[ $(git status -s) ]]; then
       git stash save "pre-commit automatic stash"
       # Run tests
       git stash pop
   fi
   ```

2. **Pipeline Integration**
   ```bash
   # Save changes before CI
   git stash save "CI: Temporary save"
   # Run CI steps
   git stash pop
   ```
