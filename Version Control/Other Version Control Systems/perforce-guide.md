# Perforce Guide

## Overview
Perforce (P4) is a centralized version control system designed for handling large binaries and codebases. It's particularly popular in game development and enterprises dealing with large files.

## Basic Concepts

### Workspace Structure
```
Client workspace
├── .p4config       # Workspace configuration
├── .p4ignore       # Ignore patterns
└── project files   # Synced files
```

### Depot Organization
```
//depot/
├── main/          # Main development
├── release/       # Release branches
└── dev/           # Development branches
```

## Core Commands

### Workspace Setup
```bash
# Create client workspace
p4 client

# Set workspace root
p4 set P4CLIENT=workspace-name

# Configure workspace
p4 set P4CONFIG=.p4config
```

### Basic Operations
```bash
# Check out files
p4 edit file.txt

# Add files
p4 add file.txt

# Delete files
p4 delete file.txt

# Submit changes
p4 submit

# Sync files
p4 sync
```

## Changelists

### Managing Changes
```bash
# Create changelist
p4 change

# Open files in changelist
p4 edit -c changelist_number file.txt

# Move between changelists
p4 reopen -c new_changelist file.txt

# Submit changelist
p4 submit -c changelist_number
```

### Pending Changes
```bash
# List pending changes
p4 changes -u username -s pending

# Describe changelist
p4 describe changelist_number

# Shelve changes
p4 shelve -c changelist_number
```

## Branching

### Branch Specifications
```bash
# Create branch spec
p4 branch branch-name

# View branch mapping
p4 branch -o branch-name

# List branches
p4 branches
```

### Integration
```bash
# Integrate changes
p4 integrate -b branch-name

# Resolve conflicts
p4 resolve

# Submit integration
p4 submit
```

## File Management

### File Operations
```bash
# Lock files
p4 lock file.txt

# Unlock files
p4 unlock file.txt

# Check file status
p4 opened

# File history
p4 filelog file.txt
```

### File Types
```bash
# Set file type
p4 edit -t binary file.bin

# Common types
text     # Text files
binary   # Binary files
unicode  # Unicode text
symlink  # Symbolic links
```

## Administration

### User Management
```bash
# Create user
p4 user username

# Set password
p4 passwd

# List users
p4 users

# Group management
p4 group groupname
```

### Permissions
```bash
# Protect table
p4 protect

# Sample protections
write user * * //depot/...
admin user admin * //...
```

## Advanced Features

### Labels
```bash
# Create label
p4 label label-name

# Label files
p4 tag -l label-name

# Sync to label
p4 sync @label-name
```

### Streams
```bash
# Create stream
p4 stream -t development //depot/dev/stream-name

# Switch to stream
p4 client -S //depot/dev/stream-name

# List streams
p4 streams
```

## Best Practices

### Workspace Management
1. **Configuration**
   ```bash
   # .p4config
   P4CLIENT=workspace-name
   P4USER=username
   P4PORT=server:port
   ```

2. **Organization**
   ```
   - Clear workspace paths
   - Regular syncs
   - Clean unused files
   - Local backup
   ```

### Submit Guidelines
1. **Changelist Description**
   ```
   [Type] Brief description

   Detailed explanation:
   - Change details
   - Impact notes
   - Testing performed

   Issue: PROJECT-123
   ```

2. **Review Process**
   ```
   - Pre-submit testing
   - Code review
   - Integration testing
   - Documentation update
   ```

## Integration

### Build Systems
```xml
<!-- Maven configuration -->
<scm>
    <connection>scm:perforce:server:port//depot/path</connection>
    <developerConnection>scm:perforce:server:port//depot/path</developerConnection>
</scm>
```

### CI/CD Integration
```yaml
# Jenkins pipeline
pipeline {
    agent any
    stages {
        stage('Sync') {
            steps {
                p4sync credential: 'p4-creds',
                       workspace: 'jenkins-${BUILD_NUMBER}'
            }
        }
    }
}
```

## Performance

### Optimization
1. **Client Side**
   ```
   - Local caching
   - Parallel syncs
   - Workspace trimming
   - File filtering
   ```

2. **Server Side**
   ```
   - Journal compression
   - Checkpoint management
   - DB maintenance
   - Network optimization
   ```

## Troubleshooting

### Common Issues
1. **Workspace Problems**
   ```bash
   # Clean workspace
   p4 clean

   # Reconcile files
   p4 reconcile

   # Verify workspace
   p4 verify
   ```

2. **Connection Issues**
   ```bash
   # Test connection
   p4 info

   # Reset client
   p4 client -f
   ```

### Recovery Operations
```bash
# Revert changes
p4 revert

# Restore shelved changes
p4 unshelve -s changelist_number

# Recover deleted files
p4 sync @changelist_number
```

## Security

### Authentication
```bash
# Set up tickets
p4 login

# Configure 2FA
p4 set P4TICKETS=path/to/tickets

# SSL connection
p4 set P4PORT=ssl:server:port
```

### Access Control
```bash
# Protections
write user * * //depot/project/...
super user admin * //...
```

## Backup and Recovery

### Backup Procedures
```bash
# Checkpoint
p4d -jc

# Journal backup
p4d -jj

# Database backup
p4d -xu
```

### Recovery Steps
```bash
# Restore from checkpoint
p4d -r directory -jr checkpoint

# Apply journal
p4d -r directory -jr journal
```
