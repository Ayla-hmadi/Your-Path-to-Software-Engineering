# Git Repository Management Guide

## Repository Creation and Setup

### Local Repository
```bash
# Initialize new repository
git init

# Initialize with specific branch name
git init --initial-branch=main

# Convert existing project
cd existing-project
git init
git add .
git commit -m "Initial commit"
```

### Remote Repository Integration
```bash
# Add remote repository
git remote add origin <repository-url>

# Push to remote
git push -u origin main

# Clone existing repository
git clone <repository-url> [directory]
git clone --depth 1 <repository-url>  # Shallow clone
git clone --branch <branch> <repository-url>  # Clone specific branch
```

## Repository Structure

### Core Components
```
repository/
├── .git/                 # Git directory
│   ├── config           # Repository config
│   ├── HEAD            # Current branch pointer
│   ├── hooks/          # Script hooks
│   ├── objects/        # Git objects
│   └── refs/           # References
├── .gitignore           # Ignore patterns
├── .gitattributes       # File attributes
└── source files         # Working directory
```

### Essential Files

#### .gitignore
```gitignore
# Example .gitignore
*.log
node_modules/
build/
.env
.DS_Store
```

#### .gitattributes
```gitattributes
# Example .gitattributes
*.txt text
*.jpg binary
*.sh text eol=lf
*.bat text eol=crlf
```

## Repository Maintenance

### Optimization
```bash
# Garbage collection
git gc
git gc --aggressive

# Compress repository
git gc --prune=now

# Clean up unnecessary files
git clean -fd

# Optimize repository
git repack -ad
```

### Health Checks
```bash
# Check repository integrity
git fsck

# Check for corruption
git fsck --full

# Verify objects
git verify-pack -v .git/objects/pack/pack-*.idx
```

## Large File Management

### Git LFS (Large File Storage)
```bash
# Install Git LFS
git lfs install

# Track large files
git lfs track "*.psd"
git lfs track "*.zip"

# Verify tracking
git lfs ls-files

# Pull LFS objects
git lfs pull
```

### Repository Size Management
```bash
# Check repository size
du -sh .git

# Find large files
git rev-list --objects --all |
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' |
  sed -n 's/^blob //p' |
  sort -rn -k2 |
  head -10
```

## Branch Management

### Organization
```bash
# List all branches
git branch -a

# Clean up merged branches
git branch --merged | egrep -v "(^\*|main|dev)" | xargs git branch -d

# Prune remote branches
git remote prune origin
```

### Branch Strategies
1. **Feature Branches**
   ```bash
   git checkout -b feature/new-feature
   git push -u origin feature/new-feature
   ```

2. **Release Branches**
   ```bash
   git checkout -b release/v1.0
   git tag -a v1.0 -m "Release version 1.0"
   ```

## Access Control

### Repository Permissions
1. **Local Settings**
   ```bash
   # Set file permissions
   chmod -R 755 .git
   
   # Configure shared repository
   git config core.sharedRepository group
   ```

2. **Remote Settings**
   - Configure access levels
   - Manage collaborators
   - Set branch protections
   - Define merge policies

## Backup Strategies

### Local Backup
```bash
# Create bare clone
git clone --bare /path/to/repo backup.git

# Mirror repository
git clone --mirror /path/to/repo

# Bundle repository
git bundle create repo.bundle --all
```

### Remote Backup
```bash
# Add backup remote
git remote add backup <backup-url>

# Push to backup
git push --mirror backup
```

## Submodules and Subtrees

### Submodules
```bash
# Add submodule
git submodule add <repository-url> path/to/submodule

# Initialize submodules
git submodule init
git submodule update

# Update all submodules
git submodule update --remote --merge
```

### Subtrees
```bash
# Add subtree
git subtree add --prefix=path/to/subtree <repository-url> <branch>

# Update subtree
git subtree pull --prefix=path/to/subtree <repository-url> <branch>

# Push subtree changes
git subtree push --prefix=path/to/subtree <repository-url> <branch>
```

## Migration and Import

### Repository Migration
```bash
# Mirror clone old repository
git clone --mirror old-repo.git

# Push to new location
cd old-repo.git
git push --mirror new-repo-url
```

### Import External Project
```bash
# Import without history
git init
git add .
git commit -m "Import project"

# Import with history
git remote add origin <old-repo>
git fetch
git reset --hard origin/main
```

## Best Practices

### Repository Organization
1. **Structure**
   - Clear directory hierarchy
   - Consistent naming
   - Modular organization
   - Documentation location

2. **Workflow**
   - Branch naming conventions
   - Commit message standards
   - Review processes
   - Release procedures

### Maintenance Schedule
1. **Regular Tasks**
   - Garbage collection
   - Backup verification
   - Permission audits
   - Size monitoring

2. **Periodic Reviews**
   - Branch cleanup
   - Access control review
   - Documentation updates
   - Performance optimization
