# Git Common Commands Reference

## Setup and Configuration

### Initial Setup
```bash
# Configure user information
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

# Configure default editor
git config --global core.editor "editor_name"

# Configure line endings
git config --global core.autocrlf true   # Windows
git config --global core.autocrlf input  # Mac/Linux
```

## Basic Commands

### Repository Initialization
```bash
# Create new repository
git init

# Clone existing repository
git clone <repository-url>
git clone <repository-url> <directory-name>
```

### Basic Workflow
```bash
# Check repository status
git status

# Stage changes
git add <file>          # Stage specific file
git add .               # Stage all changes
git add -p             # Stage changes interactively

# Commit changes
git commit -m "commit message"
git commit -a -m "commit message"  # Stage and commit all changes
git commit --amend                 # Modify last commit

# View changes
git diff               # View unstaged changes
git diff --staged      # View staged changes
git diff <commit1> <commit2>  # Compare commits
```

### History and Logs
```bash
# View commit history
git log
git log --oneline           # Compact view
git log --graph            # Show branch graph
git log -p                 # Show patches
git log --stat            # Show statistics

# Show specific commit
git show <commit-hash>
```

## Branch Operations

### Branch Management
```bash
# List branches
git branch               # Local branches
git branch -r           # Remote branches
git branch -a           # All branches

# Create branch
git branch <branch-name>
git checkout -b <branch-name>   # Create and switch
git switch -c <branch-name>     # Create and switch (new syntax)

# Switch branches
git checkout <branch-name>
git switch <branch-name>        # New syntax

# Delete branch
git branch -d <branch-name>     # Safe delete
git branch -D <branch-name>     # Force delete
```

### Merging and Rebasing
```bash
# Merge branch
git merge <branch-name>
git merge --no-ff <branch-name>  # Create merge commit
git merge --abort               # Abort merge

# Rebase
git rebase <branch-name>
git rebase -i <commit>          # Interactive rebase
git rebase --abort             # Abort rebase
```

## Remote Operations

### Remote Management
```bash
# List remotes
git remote -v

# Add remote
git remote add <name> <url>

# Remove remote
git remote remove <name>

# Rename remote
git remote rename <old> <new>
```

### Synchronization
```bash
# Fetch changes
git fetch <remote>
git fetch --all

# Pull changes
git pull <remote> <branch>
git pull --rebase         # Pull with rebase

# Push changes
git push <remote> <branch>
git push -u origin <branch>  # Set upstream
git push --force            # Force push (use carefully)
```

## Stashing

### Stash Management
```bash
# Save changes to stash
git stash
git stash save "message"
git stash -u              # Include untracked files

# List stashes
git stash list

# Apply stash
git stash apply          # Keep stash
git stash pop            # Apply and remove
git stash apply stash@{n}  # Apply specific stash

# Remove stash
git stash drop stash@{n}
git stash clear          # Remove all stashes
```

## Advanced Operations

### Tags
```bash
# Create tag
git tag <tag-name>
git tag -a <tag-name> -m "message"  # Annotated tag

# List tags
git tag
git tag -l "pattern"

# Delete tag
git tag -d <tag-name>
```

### Reset and Clean
```bash
# Reset
git reset --soft <commit>   # Keep changes staged
git reset --mixed <commit>  # Keep changes unstaged
git reset --hard <commit>   # Discard changes

# Clean
git clean -n               # Dry run
git clean -f               # Remove untracked files
git clean -fd              # Remove untracked directories
```

## Recovery Operations

### Recovery Commands
```bash
# Restore file
git restore <file>          # Discard changes
git restore --staged <file> # Unstage changes

# Reflog
git reflog                 # View reference logs
git checkout HEAD@{n}      # Go to specific state

# Cherry-pick
git cherry-pick <commit>   # Apply specific commit
```

## Inspection and Comparison

### Examining Changes
```bash
# Blame
git blame <file>           # Show who changed what
git blame -L 10,20 <file>  # Show specific lines

# Bisect
git bisect start
git bisect bad
git bisect good <commit>
git bisect reset
```

## Configuration Management

### Config Commands
```bash
# View config
git config --list
git config --list --show-origin

# Edit config
git config --global --edit
git config --local --edit

# Unset config
git config --unset <key>
```

## Best Practices

### Command Usage
1. **Regular Commands**
   - Use `git status` frequently
   - Commit atomic changes
   - Write clear commit messages
   - Pull before push

2. **Safety Measures**
   - Avoid force push
   - Create backup branches
   - Review changes before commit
   - Use meaningful branch names
