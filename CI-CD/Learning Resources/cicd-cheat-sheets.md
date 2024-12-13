# CI/CD Cheat Sheets

## Git Commands

### Basic Git Operations
```bash
# Initialize and Clone
git init                    # Create new repository
git clone <url>            # Clone repository
git remote add origin <url> # Add remote

# Basic Operations
git add .                  # Stage all changes
git commit -m "message"    # Commit changes
git push origin <branch>   # Push to remote
git pull origin <branch>   # Pull from remote

# Branch Operations
git branch                 # List branches
git checkout -b <branch>   # Create and switch branch
git merge <branch>         # Merge branch
```

## Jenkins Pipeline

### Pipeline Syntax
```groovy
// Basic Pipeline
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
```

### Common Directives
```groovy
// Environment Variables
environment {
    MAVEN_OPTS = '-Xmx3072m'
}

// Parameters
parameters {
    string(name: 'VERSION', defaultValue: '1.0')
    choice(name: 'ENV', choices: ['dev', 'staging', 'prod'])
}

// Tools
tools {
    maven 'Maven 3.8.1'
    nodejs 'Node 14.x'
}
```

## GitLab CI/CD

### Pipeline Configuration
```yaml
# Basic Pipeline
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - npm install
    - npm run build

test:
  stage: test
  script:
    - npm test

deploy:
  stage: deploy
  script:
    - deploy.sh
  only:
    - main
```

### Common Keywords
```yaml
# Job Configuration
job:
  image: node:16
  variables:
    NODE_ENV: production
  cache:
    paths:
      - node_modules/
  artifacts:
    paths:
      - dist/
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

## GitHub Actions

### Workflow Syntax
```yaml
# Basic Workflow
name: CI
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build
        run: |
          npm install
          npm run build
```

### Common Actions
```yaml
# Popular Actions
- uses: actions/setup-node@v2
  with:
    node-version: '14'

- uses: actions/cache@v2
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

- uses: docker/build-push-action@v2
  with:
    context: .
    push: true
    tags: user/app:latest
```

## Docker Commands

### Container Operations
```bash
# Basic Commands
docker build -t image:tag .       # Build image
docker run -d -p 8080:80 image    # Run container
docker ps                         # List containers
docker stop <container>           # Stop container

# Image Management
docker images                     # List images
docker pull image:tag            # Pull image
docker push image:tag            # Push image
docker rmi image:tag             # Remove image
```

## Kubernetes

### Basic Commands
```bash
# Cluster Operations
kubectl get pods                  # List pods
kubectl get deployments          # List deployments
kubectl get services             # List services
kubectl apply -f config.yaml     # Apply config

# Pod Operations
kubectl logs <pod>               # View logs
kubectl exec -it <pod> -- /bin/sh # Shell access
kubectl port-forward <pod> 8080:80 # Port forward
kubectl delete pod <pod>         # Delete pod
```

## Common Testing Commands

### Unit Testing
```bash
# JavaScript/Node.js
npm test                         # Run tests
npm run test:coverage           # Run with coverage

# Python
pytest                          # Run tests
pytest --cov=myapp tests/      # Run with coverage

# Java
mvn test                       # Run tests
mvn verify                     # Run integration tests
```

## AWS CI/CD

### CodePipeline
```yaml
# Basic Pipeline Structure
Pipeline:
  Source:
    - CodeCommit repository
    - GitHub repository
  
  Build:
    - CodeBuild project
    - Build specifications
  
  Deploy:
    - CodeDeploy
    - Elastic Beanstalk
    - ECS
```

## Monitoring and Logging

### Basic Commands
```bash
# Log Viewing
tail -f app.log                # View logs
grep "error" app.log          # Search logs
journalctl -u service         # System logs

# Monitoring
top                          # System monitor
docker stats                 # Container stats
kubectl top pods            # Pod resources
```

## Security Checks

### Common Tools
```bash
# Static Analysis
sonar-scanner               # SonarQube scan
snyk test                   # Dependency check
git secrets --scan         # Secret scanning

# Container Security
docker scan image:tag      # Scan container
trivy image image:tag     # Vulnerability scan
```

## Environment Variables
```bash
# Setting Variables
export VAR_NAME=value     # Export variable
echo "VAR_NAME=value" >> .env  # Add to .env

# CI Platform Variables
${JENKINS_HOME}           # Jenkins
$CI_PROJECT_DIR          # GitLab CI
${{ github.workspace }}  # GitHub Actions
```
