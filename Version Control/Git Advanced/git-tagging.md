# Git Tagging Guide

## Overview
Git tags are references that point to specific points in Git history. Tags are commonly used to mark release points, version numbers, and other important milestones in a project's development.

## Tag Types

### Lightweight Tags
```bash
# Create lightweight tag
git tag v1.0.0

# List lightweight tags
git tag --list

# Delete lightweight tag
git tag -d v1.0.0
```

### Annotated Tags
```bash
# Create annotated tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Create signed tag (GPG)
git tag -s v1.0.0 -m "Signed release 1.0.0"

# Verify signed tag
git tag -v v1.0.0
```

## Basic Operations

### Creating Tags
```bash
# Tag current commit
git tag v1.0.0

# Tag specific commit
git tag v1.0.0 <commit-hash>

# Tag with annotation
git tag -a v1.0.0 -m "Version 1.0.0 release"

# Tag with signature
git tag -s v1.0.0 -m "Signed version 1.0.0"
```

### Listing Tags
```bash
# List all tags
git tag

# List tags matching pattern
git tag -l "v1.0.*"

# Show tag details
git show v1.0.0

# List tags with dates
git tag --sort=taggerdate
```

## Tag Management

### Remote Operations
```bash
# Push single tag
git push origin v1.0.0

# Push all tags
git push origin --tags

# Push only annotated tags
git push origin --follow-tags

# Delete remote tag
git push origin :refs/tags/v1.0.0
```

### Local Operations
```bash
# Delete local tag
git tag -d v1.0.0

# Rename tag
git tag new-tag old-tag
git tag -d old-tag

# Move tag to different commit
git tag -f v1.0.0 <commit-hash>
```

## Versioning Strategies

### Semantic Versioning
```bash
# Major version
git tag -a v1.0.0 -m "First stable release"

# Minor version
git tag -a v1.1.0 -m "New features"

# Patch version
git tag -a v1.1.1 -m "Bug fixes"
```

### Release Candidates
```bash
# Release candidate tags
git tag -a v1.0.0-rc1 -m "Release candidate 1"
git tag -a v1.0.0-rc2 -m "Release candidate 2"

# Beta releases
git tag -a v1.0.0-beta.1 -m "Beta 1"
```

## Best Practices

### Tagging Conventions
1. **Version Format**
   ```
   Major.Minor.Patch
   v1.0.0
   release-1.0.0
   ```

2. **Special Tags**
   ```
   latest
   stable
   production
   development
   ```

### Tag Messages
1. **Content Guidelines**
   ```
   - Release version
   - Key changes
   - Breaking changes
   - Contributors
   - Release date
   ```

2. **Format Example**
   ```
   Version 1.0.0
   
   Features:
   - Feature A
   - Feature B
   
   Bug Fixes:
   - Fix X
   - Fix Y
   
   Contributors:
   - Name <email>
   ```

## Release Management

### Release Process
1. **Preparation**
   ```bash
   # Update version files
   # Run tests
   # Update documentation
   git add .
   git commit -m "Prepare for release 1.0.0"
   ```

2. **Creating Release**
   ```bash
   git tag -a v1.0.0 -m "Release 1.0.0"
   git push origin main --tags
   ```

### Release Documentation
1. **Change Log**
   ```markdown
   # Change Log
   
   ## [1.0.0] - 2024-01-20
   ### Added
   - Feature A
   - Feature B
   
   ### Fixed
   - Issue X
   - Issue Y
   ```

2. **Release Notes**
   ```markdown
   # Release Notes v1.0.0
   
   ## New Features
   ## Bug Fixes
   ## Breaking Changes
   ## Upgrade Guide
   ```

## CI/CD Integration

### Automated Releases
```yaml
# GitHub Actions example
name: Release
on:
  push:
    tags:
      - 'v*'
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Create Release
        uses: actions/create-release@v1
```

### Version Detection
```bash
# Get latest tag
git describe --tags --abbrev=0

# Get current version
git describe --tags
```

## Advanced Operations

### Tag Filtering
```bash
# List tags by date
git tag --sort=-creatordate

# Find tags containing commit
git tag --contains <commit-hash>

# Search tag messages
git tag -l -n1 | grep "pattern"
```

### Tag Maintenance
```bash
# Clean up old tags
git tag -l | xargs git tag -d    # Delete all local tags
git fetch --tags                 # Refresh from remote

# Verify all signed tags
git tag -l | xargs git tag -v
```

## Troubleshooting

### Common Issues
1. **Tag Conflicts**
   ```bash
   # Force update tag
   git tag -f v1.0.0 <commit-hash>
   git push origin :refs/tags/v1.0.0
   git push origin v1.0.0
   ```

2. **Missing Tags**
   ```bash
   # Fetch missing tags
   git fetch --all --tags --prune
   ```

### Recovery
```bash
# Find deleted tags
git fsck --unreachable | grep tag

# Restore tag
git tag v1.0.0 <tag-hash>
```

## Security Considerations

### Signed Tags
1. **Setup GPG**
   ```bash
   # Generate GPG key
   gpg --gen-key
   
   # Configure Git
   git config --global user.signingkey <key-id>
   ```

2. **Sign Tags**
   ```bash
   # Create signed tag
   git tag -s v1.0.0 -m "Signed release 1.0.0"
   
   # Verify signature
   git tag -v v1.0.0
   ```
