# GitHub Actions Common Workflows

## Node.js Application Workflows

### Basic Node.js CI
```yaml
name: Node.js CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    
    - run: npm ci
    - run: npm run build --if-present
    - run: npm test
```

### Full Stack Deployment
```yaml
name: Full Stack Deploy

on:
  push:
    branches: [ main ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Frontend Build
      run: |
        cd frontend
        npm install
        npm run build
    
    - name: Backend Build
      run: |
        cd backend
        npm install
        npm run build
    
    - name: Deploy to AWS
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1
    
    - run: |
        aws s3 sync frontend/build/ s3://my-bucket
        aws elasticbeanstalk update-environment
```

## Docker Workflows

### Docker Build and Push
```yaml
name: Docker Build

on:
  push:
    tags: ['v*']

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Login to DockerHub
      uses: docker/login-action@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
    
    - name: Build and push
      uses: docker/build-push-action@v2
      with:
        context: .
        push: true
        tags: |
          user/app:latest
          user/app:${{ github.ref_name }}
```

### Multi-Platform Docker Build
```yaml
name: Multi-Platform Docker

on:
  release:
    types: [published]

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up QEMU
      uses: docker/setup-qemu-action@v1
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1
    
    - name: Build and push
      uses: docker/build-push-action@v2
      with:
        platforms: linux/amd64,linux/arm64
        push: true
        tags: user/app:latest
```

## Testing Workflows

### Comprehensive Testing
```yaml
name: Test Suite

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:13
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Unit Tests
      run: npm run test:unit
    
    - name: Integration Tests
      run: npm run test:integration
    
    - name: E2E Tests
      run: npm run test:e2e
    
    - name: Upload Coverage
      uses: codecov/codecov-action@v2
```

## Release Workflows

### Automated Release
```yaml
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
    
    - name: Build
      run: |
        npm install
        npm run build
    
    - name: Create Release
      uses: softprops/action-gh-release@v1
      with:
        files: |
          dist/*.zip
          dist/*.tar.gz
        body_path: CHANGELOG.md
        draft: false
        prerelease: false
```

## Security Workflows

### Security Scanning
```yaml
name: Security Scan

on:
  push:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * 0'

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Run Snyk
      uses: snyk/actions/node@master
      env:
        SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
    
    - name: Run OWASP ZAP
      uses: zaproxy/action-full-scan@v0.3.0
      with:
        target: 'https://www.example.com'
```

## Documentation Workflows

### Documentation Deploy
```yaml
name: Deploy Docs

on:
  push:
    branches: [ main ]
    paths:
      - 'docs/**'
      - 'mkdocs.yml'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.x'
    
    - name: Install MkDocs
      run: |
        pip install mkdocs-material
        pip install mkdocs-minify-plugin
    
    - name: Deploy
      run: mkdocs gh-deploy --force
```

## Maintenance Workflows

### Dependency Updates
```yaml
name: Dependencies

on:
  schedule:
    - cron: '0 0 * * 1'  # Every Monday
  workflow_dispatch:

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Update Dependencies
      uses: renovatebot/github-action@v32
      with:
        configurationFile: renovate.json
        token: ${{ secrets.GITHUB_TOKEN }}
```

### Cleanup Actions
```yaml
name: Cleanup

on:
  schedule:
    - cron: '0 0 * * 0'  # Every Sunday

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
    - name: Delete old artifacts
      uses: actions/github-script@v5
      with:
        script: |
          const days = 7;
          const ms = days * 24 * 60 * 60 * 1000;
          const timestamp = new Date(Date.now() - ms).toISOString();
          
          const artifacts = await github.rest.actions.listArtifactsForRepo({
            owner: context.repo.owner,
            repo: context.repo.repo,
          });
          
          for (const artifact of artifacts.data.artifacts) {
            if (artifact.created_at < timestamp) {
              await github.rest.actions.deleteArtifact({
                owner: context.repo.owner,
                repo: context.repo.repo,
                artifact_id: artifact.id,
              });
            }
          }
```
