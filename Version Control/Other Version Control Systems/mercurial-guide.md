# Mercurial (Hg) Guide

## Overview
Mercurial is a distributed version control system that emphasizes simplicity, efficiency, and robust handling of both files and changesets. It provides an intuitive command set while maintaining powerful functionality.

## Basic Concepts

### Repository Structure
```
.hg/           # Repository data
.hgignore      # Ignore patterns
.hgtags        # Tag definitions
working files  # Project files
```

### Changesets
```
- Atomic commits
- Complete snapshots
- Immutable history
- Cryptographic hashing
```

## Core Commands

### Repository Setup
```bash
# Create new repository
hg init [directory]

# Clone repository
hg clone <url> [directory]

# Pull changes
hg pull
hg pull <remote>
```

### Basic Operations
```bash
# Check status
hg status

# Add files
hg add [file]

# Commit changes
hg commit -m "message"
hg ci -m "message"

# Update working directory
hg update
hg up
```

## Branching

### Branch Types
1. **Named Branches**
   ```bash
   # Create branch
   hg branch feature-x
   
   # List branches
   hg branches
   
   # Switch branch
   hg update feature-x
   ```

2. **Bookmarks**
   ```bash
   # Create bookmark
   hg bookmark feature-y
   
   # List bookmarks
   hg bookmarks
   
   # Switch bookmark
   hg update feature-y
   ```

### Merging
```bash
# Merge changes
hg merge feature-x

# Resolve conflicts
hg resolve --mark file.txt

# Complete merge
hg commit -m "Merge feature-x"
```

## History and Logs

### Viewing History
```bash
# View log
hg log
hg log -l 5    # Last 5 commits
hg log -v      # Verbose
hg log -p      # With patches

# Graph view
hg log --graph
```

### Querying Changes
```bash
# Search commits
hg log -k "keyword"

# Filter by user
hg log -u username

# Date range
hg log -d "2024-01-01 to 2024-01-31"
```

## Advanced Features

### Phases
```bash
# Change phase
hg phase --public
hg phase --draft
hg phase --secret

# View phases
hg log --template "{phase}\n"
```

### Extensions
```ini
# .hgrc configuration
[extensions]
color =
shelve =
rebase =
purge =
```

## Repository Management

### Remote Operations
```bash
# Add remote
hg paths
hg path --add name url

# Push changes
hg push
hg push remote

# Pull changes
hg pull
hg pull remote
```

### Tags and Releases
```bash
# Create tag
hg tag v1.0.0

# List tags
hg tags

# Remove tag
hg tag --remove v1.0.0
```

## Working with Changes

### Shelving Changes
```bash
# Shelve changes
hg shelve
hg shelve --name feature-work

# List shelved changes
hg shelve --list

# Restore shelved changes
hg unshelve
hg unshelve feature-work
```

### Reverting Changes
```bash
# Revert file
hg revert file.txt

# Revert all changes
hg revert --all

# Clean working directory
hg purge
```

## Best Practices

### Commit Guidelines
1. **Message Format**
   ```
   Summary line (50 chars or less)

   More detailed explanatory text
   - Change description
   - Impact notes
   - Related issues
   ```

2. **Change Organization**
   ```
   - Single purpose
   - Complete changes
   - Related files
   - Proper testing
   ```

### Workflow Patterns
1. **Feature Development**
   ```bash
   hg pull
   hg update default
   hg branch feature-x
   # Make changes
   hg commit
   hg push
   ```

2. **Release Process**
   ```bash
   hg update default
   hg merge feature-x
   hg commit -m "Merge feature-x"
   hg tag v1.0.0
   hg push
   ```

## Integration

### Build Tools
```xml
<!-- Maven configuration -->
<scm>
    <connection>scm:hg:http://server/repo</connection>
    <developerConnection>scm:hg:https://server/repo</developerConnection>
    <url>http://server/repo</url>
</scm>
```

### CI/CD Integration
```yaml
# Jenkins pipeline
pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                hg clone ${repo_url}
            }
        }
    }
}
```

## Troubleshooting

### Common Issues
1. **Repository Problems**
   ```bash
   # Verify repository
   hg verify
   
   # Recover repository
   hg recover
   ```

2. **Merge Conflicts**
   ```bash
   # List conflicts
   hg resolve --list
   
   # Mark resolved
   hg resolve --mark file.txt
   ```

### Recovery Operations
```bash
# Rollback last transaction
hg rollback

# Strip changesets
hg strip <rev>

# Backup repository
hg bundle ../backup.hg
```

## Migration

### From Other VCS
1. **From Git**
   ```bash
   # Using hg-git
   hg clone git://repo.git
   ```

2. **To Git**
   ```bash
   # Using fast-export
   hg-fast-export -r hg_repo
   ```

## Security

### Access Control
```ini
# .hg/hgrc
[web]
allow_push = user1, user2
deny_push = *

[auth]
repo.schemes = https
repo.prefix = https://repo
repo.username = user
repo.password = pass
```

### Authentication
```bash
# Configure authentication
hg config --edit

# Use SSH keys
hg clone ssh://user@host/repo
```
