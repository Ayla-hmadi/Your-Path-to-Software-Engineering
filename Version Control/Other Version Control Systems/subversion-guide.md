# Apache Subversion (SVN) Guide

## Overview
Apache Subversion (SVN) is a centralized version control system focusing on simplicity and reliability. It tracks changes to files and directories over time, maintaining a single central repository.

## Basic Concepts

### Repository Structure
```
repository/
├── trunk/      # Main development line
├── branches/   # Feature development
└── tags/       # Release snapshots
```

### Working Copy
```bash
# Check out repository
svn checkout <url> [path]
svn co <url> [path]

# Update working copy
svn update
svn up
```

## Essential Commands

### Basic Operations
```bash
# Add files
svn add file.txt
svn add *

# Commit changes
svn commit -m "message"
svn ci -m "message"

# Update to latest
svn update
```

### Status and Information
```bash
# Check status
svn status
svn st

# View changes
svn diff
svn diff -r 123
svn diff -r 123:456

# View history
svn log
svn log -v
svn log -r 123:456
```

## Branch Management

### Creating Branches
```bash
# Create branch
svn copy trunk/ branches/feature-x
svn cp trunk/ branches/feature-x

# Switch to branch
svn switch ^/branches/feature-x
```

### Merging
```bash
# Merge changes
svn merge ^/trunk
svn merge -r 123:456 ^/branches/feature-x

# Resolve conflicts
svn resolve --accept working file.txt
```

## Repository Administration

### Repository Creation
```bash
# Create new repository
svnadmin create /path/to/repo

# Import existing project
svn import project-dir file:///path/to/repo/trunk
```

### Maintenance
```bash
# Verify repository
svnadmin verify /path/to/repo

# Backup repository
svnadmin dump /path/to/repo > backup.dump

# Load backup
svnadmin load /path/to/repo < backup.dump
```

## Access Control

### Authentication
```apache
# Apache configuration
<Location /svn>
  DAV svn
  SVNPath /path/to/repo
  AuthType Basic
  AuthName "SVN Repository"
  AuthUserFile /path/to/htpasswd
  Require valid-user
</Location>
```

### Authorization
```ini
# svnserve.conf
[general]
anon-access = none
auth-access = write
password-db = passwd
authz-db = authz

# authz file
[/]
* = r
admin = rw
```

## Working with Files

### File Operations
```bash
# Add files
svn add file.txt

# Delete files
svn delete file.txt
svn rm file.txt

# Move files
svn move old.txt new.txt
svn mv old.txt new.txt

# Copy files
svn copy src.txt dest.txt
svn cp src.txt dest.txt
```

### Property Management
```bash
# Set properties
svn propset svn:ignore "*.tmp" .
svn pset svn:keywords "Id Rev" file.txt

# List properties
svn proplist
svn pl

# Get property value
svn propget svn:ignore
svn pg svn:ignore
```

## Integration

### IDE Integration
1. **Eclipse**
   ```
   - Subversive plugin
   - Subclipse plugin
   - Team synchronization
   - Visual merge
   ```

2. **IntelliJ IDEA**
   ```
   - Built-in support
   - Change management
   - Branch visualization
   - Merge tools
   ```

### Build Tools
```xml
<!-- Maven SCM configuration -->
<scm>
    <connection>scm:svn:http://server/repo</connection>
    <developerConnection>scm:svn:https://server/repo</developerConnection>
    <url>http://server/repo</url>
</scm>
```

## Best Practices

### Repository Organization
1. **Standard Layout**
   ```
   trunk/        # Main development
   branches/     # Feature work
   tags/         # Releases
   ```

2. **Branch Naming**
   ```
   branches/feature-x
   branches/bugfix-y
   branches/release-1.0
   ```

### Commit Guidelines
1. **Message Format**
   ```
   [Type] Brief description

   Detailed explanation
   - Change 1
   - Change 2

   Issue: PROJECT-123
   ```

2. **Change Scope**
   ```
   - Single purpose
   - Complete changes
   - Related files
   - Test inclusion
   ```

## Migration

### To Git
```bash
# Using git-svn
git svn clone <svn-url>

# Preserve history
git svn clone --stdlayout <svn-url>

# With authors
git svn clone --authors-file=authors.txt <svn-url>
```

### Data Export
```bash
# Export revision
svn export -r 123 <url> [path]

# Export latest
svn export <url> [path]

# Export working copy
svn export /path/to/wc /path/to/export
```

## Troubleshooting

### Common Issues
1. **Working Copy**
   ```bash
   # Clean up
   svn cleanup

   # Resolve conflicts
   svn resolve --accept working file.txt

   # Break locks
   svn cleanup --remove-unversioned
   ```

2. **Network Issues**
   ```bash
   # Check server
   svn info <url>

   # Reset credentials
   svn auth --remove
   ```

### Recovery
```bash
# Revert changes
svn revert file.txt
svn revert -R directory

# Fix tree conflicts
svn resolve --accept working-all

# Repair working copy
svn cleanup --vacuum-pristines
```
