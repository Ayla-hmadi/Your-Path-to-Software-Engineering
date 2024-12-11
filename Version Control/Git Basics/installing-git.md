# Installing Git

## Platform-Specific Installation

### Windows Installation

#### Method 1: Git for Windows (Recommended)
1. **Download**
   - Visit https://git-scm.com/download/windows
   - Download Git for Windows installer
   - Choose 64-bit or 32-bit version

2. **Installation Steps**
   ```
   1. Run the installer
   2. Read and accept license terms
   3. Choose installation location
   4. Select components:
      - Git Bash
      - Git GUI
      - Git LFS
      - Associate .git* files
   5. Choose default editor
   6. Adjust PATH environment
   7. Choose HTTPS transport backend
   8. Configure line ending conversions
   9. Configure terminal emulator
   10. Configure extra options
   ```

#### Method 2: Windows Package Manager
```powershell
# Using winget
winget install --id Git.Git -e --source winget

# Using Chocolatey
choco install git
```

### macOS Installation

#### Method 1: Homebrew (Recommended)
```bash
# Install Homebrew if not installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Git
brew install git
```

#### Method 2: Direct Download
1. **Download**
   - Visit https://git-scm.com/download/mac
   - Download macOS installer
   - Run the package installer

#### Method 3: Xcode Command Line Tools
```bash
# Install command line tools
xcode-select --install
```

### Linux Installation

#### Debian/Ubuntu
```bash
# Update package list
sudo apt update

# Install Git
sudo apt install git
```

#### Red Hat/Fedora
```bash
# Using dnf
sudo dnf install git

# Using yum (older versions)
sudo yum install git
```

#### SUSE/OpenSUSE
```bash
sudo zypper install git
```

## Post-Installation Setup

### Initial Configuration
```bash
# Set user name
git config --global user.name "Your Name"

# Set email address
git config --global user.email "your.email@example.com"

# Set default editor
git config --global core.editor "editor-name"

# Set default branch name
git config --global init.defaultBranch main
```

### SSH Setup
1. **Generate SSH Key**
   ```bash
   ssh-keygen -t ed25519 -C "your.email@example.com"
   ```

2. **Start SSH Agent**
   ```bash
   # On Linux/macOS
   eval "$(ssh-agent -s)"

   # On Windows (Git Bash)
   eval $(ssh-agent)
   ```

3. **Add SSH Key**
   ```bash
   ssh-add ~/.ssh/id_ed25519
   ```

## Configuration Options

### System-wide Configuration
```bash
# Location: /etc/gitconfig
git config --system setting.name value
```

### User Configuration
```bash
# Location: ~/.gitconfig
git config --global setting.name value
```

### Repository Configuration
```bash
# Location: .git/config
git config --local setting.name value
```

## Common Configurations

### Basic Settings
```bash
# Set color UI
git config --global color.ui auto

# Set pull strategy
git config --global pull.rebase false

# Set push behavior
git config --global push.default simple

# Set credential helper
git config --global credential.helper cache
```

### Aliases
```bash
# Add common aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

## Verification

### Check Installation
```bash
# Check Git version
git --version

# Check configuration
git config --list

# Check specific setting
git config user.name
git config user.email
```

## Troubleshooting

### Common Issues

#### Windows
1. **Path Issues**
   ```bash
   # Add Git to PATH manually
   setx PATH "%PATH%;C:\Program Files\Git\bin"
   ```

2. **Line Ending Issues**
   ```bash
   # Configure line endings
   git config --global core.autocrlf true
   ```

#### macOS
1. **Xcode Tools**
   ```bash
   # Verify installation
   xcode-select -p
   
   # Reinstall if needed
   xcode-select --install
   ```

#### Linux
1. **Permission Issues**
   ```bash
   # Fix permissions
   sudo chown -R $USER:$USER ~/.git
   ```

## Additional Tools

### GUI Clients
1. **Popular Options**
   - GitHub Desktop
   - Sourcetree
   - GitKraken
   - Fork

2. **Installation**
   ```bash
   # Example: GitHub Desktop
   # Windows/macOS: Download from website
   # Linux:
   sudo apt install github-desktop
   ```

### IDE Integration
1. **Visual Studio Code**
   - Built-in Git support
   - Source Control panel
   - Git lens extension

2. **JetBrains IDEs**
   - Built-in Git integration
   - Version Control tool window
   - Git flow support

## Next Steps
1. Basic Git usage
2. Repository creation
3. Branch management
4. Remote repository setup
