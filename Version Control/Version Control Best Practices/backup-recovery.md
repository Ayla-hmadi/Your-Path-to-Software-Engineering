# Git Backup and Recovery Guide

## Backup Strategies

### Local Repository Backup
```bash
# Create bare clone
git clone --bare /path/to/repo backup.git

# Mirror clone (includes all refs)
git clone --mirror /path/to/repo

# Bundle repository
git bundle create repo.bundle --all
```

### Remote Repository Backup
```bash
# Add backup remote
git remote add backup <backup-url>

# Push all refs
git push --mirror backup

# Push all tags
git push --tags backup
```

## Backup Types

### Full Repository Backup
1. **Including All Refs**
   ```bash
   # Clone with all references
   git clone --mirror origin-url backup-dir
   
   # Update backup
   cd backup-dir
   git fetch origin --prune
   ```

2. **Bundle Creation**
   ```bash
   # Create full bundle
   git bundle create ../backup.bundle --all
   
   # Verify bundle
   git bundle verify ../backup.bundle
   ```

### Incremental Backup
```bash
# Create incremental bundle
git bundle create ../inc.bundle master ^master@{1.week.ago}

# Update specific refs
git push backup refs/heads/*:refs/heads/*
```

## Recovery Procedures

### Lost Commits
1. **Using Reflog**
   ```bash
   # View reflog
   git reflog

   # Recover commit
   git checkout -b recovery HEAD@{2}
   ```

2. **Using FSCheck**
   ```bash
   # Find dangling commits
   git fsck --full --no-reflogs

   # Recover commit
   git checkout -b recover <commit-hash>
   ```

### Branch Recovery
```bash
# Find deleted branch tip
git reflog

# Recreate branch
git branch <branch-name> <commit-hash>

# Recover from backup
git fetch backup <branch-name>
```

## Data Protection

### Repository Integrity
1. **Regular Checks**
   ```bash
   # Check repository health
   git fsck

   # Verify objects
   git fsck --full

   # Check connectivity
   git fsck --connectivity-only
   ```

2. **Maintenance**
   ```bash
   # Clean unnecessary files
   git gc

   # Optimize repository
   git gc --aggressive

   # Prune old objects
   git prune
   ```

### Security Measures
1. **Access Control**
   ```bash
   # Configure permissions
   chmod -R 700 .git

   # Set group permissions
   chgrp -R dev-team .git
   ```

2. **Encryption**
   ```bash
   # Encrypt backup
   tar czf - repo.git | gpg -e > backup.tar.gz.gpg

   # Decrypt backup
   gpg -d backup.tar.gz.gpg | tar xzf -
   ```

## Automated Backup

### Scheduled Backups
1. **Cron Job Setup**
   ```bash
   # Daily backup script
   #!/bin/bash
   cd /path/to/repo
   git bundle create ../backup-$(date +%F).bundle --all
   ```

2. **Retention Policy**
   ```bash
   # Keep last 30 days
   find /backup/dir -name "backup-*.bundle" -mtime +30 -delete
   ```

### Continuous Backup
```bash
# Post-receive hook
#!/bin/bash
git push --mirror backup

# Automated bundle creation
git bundle create ../$(date +%F-%H).bundle --all
```

## Disaster Recovery

### Complete Repository Loss
1. **From Mirror**
   ```bash
   # Clone from backup
   git clone --mirror backup-url new-repo
   
   # Convert to working copy
   git config --bool core.bare false
   git checkout main
   ```

2. **From Bundle**
   ```bash
   # Create new repository
   git init
   
   # Extract from bundle
   git pull ../backup.bundle
   ```

### Partial Recovery
1. **Specific Branches**
   ```bash
   # Fetch specific branch
   git fetch backup branch-name

   # Recover specific tags
   git fetch backup refs/tags/*
   ```

2. **Selected Commits**
   ```bash
   # Cherry-pick commits
   git cherry-pick <commit-hash>

   # Apply range of commits
   git rebase --onto main <start-commit> <end-commit>
   ```

## Emergency Procedures

### Quick Recovery Steps
```bash
# 1. Stop current operations
git merge --abort  # If merging
git rebase --abort # If rebasing

# 2. Create backup of current state
git branch backup-$(date +%F-%H-%M)

# 3. Check repository state
git status
git fsck

# 4. Begin recovery
git reset --hard backup/main  # If backup exists
```

### Data Salvage
```bash
# Find all commits
git fsck --lost-found

# Examine lost commits
git show <commit-hash>

# Recover files
git checkout <commit-hash> -- path/to/file
```

## Best Practices

### Backup Strategy
1. **Multiple Backups**
   ```
   - Local mirrors
   - Remote repositories
   - Bundle archives
   - Different locations
   ```

2. **Regular Testing**
   ```
   - Verify backups
   - Test recovery
   - Update procedures
   - Document process
   ```

### Recovery Preparation
1. **Documentation**
   ```
   - Recovery procedures
   - Contact information
   - Access credentials
   - System details
   ```

2. **Regular Drills**
   ```
   - Practice recovery
   - Update procedures
   - Train team members
   - Review results
   ```
