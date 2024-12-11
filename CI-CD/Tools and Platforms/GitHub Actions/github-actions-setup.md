# GitHub Actions Setup and Configuration

## Repository Setup

### Enabling Actions
```yaml
Repository Settings:
  1. Settings → Actions → General
  2. Choose access level:
     - Allow all actions
     - Allow selected actions
     - Disable actions

Security Options:
  - Required workflows
  - Fork pull request workflows
  - External workflow permissions
```

### Directory Structure
```
repository/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── deploy.yml
│   │   └── release.yml
│   ├── actions/
│   └── CODEOWNERS
├── src/
└── tests/
```

## Workflow Configuration

### Basic Setup
```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup
        run: |
          npm install
          npm test
```

### Environment Configuration
```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://production.example.com
    
    env:
      NODE_ENV: production
      API_URL: ${{ secrets.API_URL }}
    
    steps:
      - uses: actions/checkout@v2
      - name: Deploy
        run: npm run deploy
```

## Runner Setup

### GitHub-Hosted Runners
```yaml
Available Runners:
  - ubuntu-latest
  - windows-latest
  - macos-latest

Configuration:
  runs-on: ubuntu-latest
  runs-on: [self-hosted, linux, x64]
```

### Self-Hosted Runners
```bash
# Runner Installation
# 1. Download runner
$ ./config.sh --url https://github.com/org/repo --token TOKEN

# 2. Configure runner
$ ./config.sh --name "custom-runner" --labels custom,gpu

# 3. Start runner
$ ./run.sh
```

## Security Configuration

### Secret Management
1. **Repository Secrets**
   ```yaml
   Path: Settings → Secrets → Actions
   
   Usage:
     env:
       TOKEN: ${{ secrets.API_TOKEN }}
   ```

2. **Environment Secrets**
   ```yaml
   Setup:
     1. Create environment
     2. Configure protection rules
     3. Add environment secrets
   
   Usage:
     environment:
       name: production
       url: ${{ steps.deploy.outputs.url }}
   ```

### Permission Configuration
```yaml
permissions:
  contents: read
  issues: write
  pull-requests: write

jobs:
  security:
    permissions:
      security-events: write
      actions: read
```

## Resource Management

### Job Configuration
```yaml
jobs:
  build:
    timeout-minutes: 60
    continue-on-error: false
    concurrency:
      group: production
      cancel-in-progress: true
```

### Cache Configuration
```yaml
steps:
  - uses: actions/cache@v2
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-node-
```

## Event Configuration

### Webhook Events
```yaml
on:
  push:
    branches:
      - main
      - 'releases/**'
    paths:
      - 'src/**'
      - '!**.md'
  
  pull_request:
    types: [opened, synchronize]
```

### Scheduled Events
```yaml
on:
  schedule:
    - cron: '0 0 * * *'  # Daily
    - cron: '*/15 * * * *'  # Every 15 minutes
```

## Matrix Builds

### Basic Matrix
```yaml
jobs:
  test:
    strategy:
      matrix:
        node: [14, 16, 18]
        os: [ubuntu-latest, windows-latest]
    
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node }}
```

### Complex Matrix
```yaml
strategy:
  matrix:
    node: [14, 16]
    os: [ubuntu, windows]
    include:
      - os: ubuntu
        node: 18
        experimental: true
    exclude:
      - os: windows
        node: 14
```

## Artifact Management

### Upload Configuration
```yaml
steps:
  - uses: actions/upload-artifact@v2
    with:
      name: build-files
      path: |
        dist/
        !dist/**/*.map
      retention-days: 5
```

### Download Configuration
```yaml
steps:
  - uses: actions/download-artifact@v2
    with:
      name: build-files
      path: dist
```

## Monitoring Setup

### Action Logs
```yaml
steps:
  - name: Enable Debug Logging
    env:
      ACTIONS_STEP_DEBUG: true
    run: echo "Debug logging enabled"
```

### Status Badges
```markdown
[![CI](https://github.com/org/repo/workflows/CI/badge.svg)](https://github.com/org/repo/actions)
```

## Advanced Configuration

### Composite Actions
```yaml
# .github/actions/setup/action.yml
name: 'Setup Environment'
runs:
  using: composite
  steps:
    - run: npm install
      shell: bash
    - run: npm run build
      shell: bash
```

### Reusable Workflows
```yaml
# .github/workflows/reusable.yml
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ inputs.environment }}
```
