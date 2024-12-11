# Git Rebasing vs Merging Guide

## Overview

### Merging
- Combines branches by creating a new commit
- Preserves complete history
- Non-destructive operation
- Creates explicit merge commits
- Maintains original branch topology

### Rebasing
- Replays commits on top of another branch
- Rewrites commit history
- Creates linear history
- Eliminates merge commits
- Changes commit hashes

## Detailed Comparison

### Merge Process
```bash
# Standard merge
git checkout main
git merge feature

# Result:
*   e123456 (HEAD -> main) Merge branch 'feature'
|\
| * d123456 (feature) Feature commit 2
| * c123456 Feature commit 1
|/
* b123456 Main commit 2
* a123456 Initial commit
```

### Rebase Process
```bash
# Standard rebase
git checkout feature
git rebase main

# Result:
* f123456 (HEAD -> feature) Feature commit 2
* e123456 Feature commit 1
* d123456 (main) Main commit 2
* a123456 Initial commit
```

## Use Cases

### When to Merge
1. **Collaborative Features**
   - Multiple developers
   - Long-running branches
   - Feature integration
   - Public branches

2. **Documentation Needs**
   - Clear feature history
   - Integration points
   - Release tracking
   - Audit requirements

### When to Rebase
1. **Local Branches**
   - Personal work
   - Feature cleanup
   - Branch updates
   - Private changes

2. **Clean History**
   - Linear timeline
   - Feature isolation
   - Clear progression
   - Simplified logs

## Command Reference

### Merge Commands
```bash
# Basic merge
git merge feature

# No-fast-forward merge
git merge --no-ff feature

# Squash merge
git merge --squash feature

# Abort merge
git merge --abort
```

### Rebase Commands
```bash
# Basic rebase
git rebase main

# Interactive rebase
git rebase -i main

# Continue after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort

# Skip current patch
git rebase --skip
```

## Advanced Operations

### Interactive Rebasing
```bash
# Start interactive rebase
git rebase -i HEAD~3

# Available commands:
pick    # Use commit as is
reword  # Edit commit message
edit    # Stop for amending
squash  # Combine with previous
fixup   # Combine and discard message
drop    # Remove commit
```

### Complex Merges
```bash
# Merge strategies
git merge -s recursive feature
git merge -s ours feature
git merge -s theirs feature

# Merge options
git merge -X ignore-space-change
git merge -X theirs
git merge -X ours
```

## Best Practices

### Merging Guidelines
1. **Before Merging**
   - Update local branch
   - Review changes
   - Run tests
   - Clean working directory

2. **During Merge**
   - Handle conflicts carefully
   - Maintain feature integrity
   - Verify functionality
   - Document decisions

### Rebasing Guidelines
1. **Golden Rules**
   - Never rebase public branches
   - Create backup branches
   - Understand history changes
   - Communicate with team

2. **Best Practices**
   - Rebase before pushing
   - Keep rebases small
   - Handle conflicts carefully
   - Test after rebasing

## Conflict Resolution

### Merge Conflicts
```bash
# During merge conflict
git status                  # Check status
git diff                    # View conflicts
git checkout --ours file    # Keep our version
git checkout --theirs file  # Keep their version
git add file               # Mark as resolved
git merge --continue       # Complete merge
```

### Rebase Conflicts
```bash
# During rebase conflict
git status                 # Check status
git diff                   # View conflicts
git checkout --ours file   # Keep base version
git checkout --theirs file # Keep feature version
git add file              # Mark as resolved
git rebase --continue     # Continue rebase
```

## Common Issues

### Merge Problems
1. **Complex Conflicts**
   - Multiple contributors
   - Parallel changes
   - Directory conflicts
   - Binary files

2. **Resolution Steps**
   - Identify conflict source
   - Understand changes
   - Choose resolution strategy
   - Verify results

### Rebase Problems
1. **Lost Commits**
   - Interrupted rebase
   - Force push issues
   - Reference loss
   - History divergence

2. **Recovery Steps**
   - Check reflog
   - Restore commits
   - Reset branches
   - Communicate changes

## Team Workflows

### Merge-Based Workflow
1. **Feature Branches**
   - Create from main
   - Regular updates
   - Merge when complete
   - Delete after merge

2. **Integration Strategy**
   - Code review
   - CI/CD validation
   - Merge commits
   - Version tracking

### Rebase-Based Workflow
1. **Feature Development**
   - Create feature branch
   - Regular rebases
   - Clean commits
   - Linear history

2. **Integration Strategy**
   - Rebase on main
   - Squash commits
   - Fast-forward merge
   - Clean branch deletion

## Decision Guide

### Choose Merge When
- Working on public branches
- Preserving feature context
- Multiple collaborators
- Clear integration points
- Audit requirements

### Choose Rebase When
- Working on private branches
- Cleaning up local work
- Maintaining linear history
- Preparing for pull requests
- Incorporating upstream changes
