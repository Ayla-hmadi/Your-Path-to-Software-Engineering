# GitLab CI Setup and Configuration Guide

## Initial Setup

### Repository Configuration
```yaml
# .gitlab-ci.yml placement
project_root/
├── .gitlab-ci.yml
├── src/
└── tests/

# Basic configuration
image: alpine:latest
stages:
  - build
  - test
  - deploy
```

### Runner Installation
```bash
# Docker Runner Installation
docker run -d \
  --name gitlab-runner \
  --restart always \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:latest

# Register Runner
gitlab-runner register \
  --url "https://gitlab.example.com/" \
  --registration-token "PROJECT_REGISTRATION_TOKEN" \
  --description "docker-runner" \
  --docker-image "docker:stable" \
  --executor docker
```

## Runner Configuration

### Executor Types
```yaml
# Shell Executor
shell_job:
  script:
    - echo "Running in shell"

# Docker Executor
docker_job:
  image: node:16
  script:
    - npm install
    - npm test

# Kubernetes Executor
kubernetes_job:
  image: 
    name: golang:latest
    entrypoint: [""]
  script:
    - go test ./...
```

### Runner Tags
```yaml
job_with_tags:
  tags:
    - docker
    - production
  script:
    - echo "Running with specific tags"

# Runner configuration
[[runners]]
  name = "docker-runner"
  tags = ["docker", "production"]
```

## Pipeline Configuration

### Basic Structure
```yaml
variables:
  GLOBAL_VAR: "value"

default:
  image: ubuntu:latest
  before_script:
    - apt-get update

stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - make build

test:
  stage: test
  script:
    - make test

deploy:
  stage: deploy
  script:
    - make deploy
```

### Environment Configuration
```yaml
# Environment variables
variables:
  DB_HOST: "db.example.com"
  API_TOKEN: $CI_JOB_TOKEN

# Environment-specific jobs
deploy_staging:
  environment:
    name: staging
    url: https://staging.example.com
  script:
    - deploy staging

deploy_production:
  environment:
    name: production
    url: https://example.com
  when: manual
```

## Cache and Artifacts

### Cache Configuration
```yaml
# Global cache
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .yarn-cache/
  policy: pull-push

# Job-specific cache
build:
  cache:
    key: 
      files: 
        - package-lock.json
    paths:
      - node_modules/
    policy: pull
```

### Artifact Management
```yaml
build:
  artifacts:
    paths:
      - dist/
      - build/
    reports:
      junit: test-results.xml
      coverage: coverage/
      codequality: gl-code-quality-report.json
    expire_in: 1 week
    when: on_success
```

## Security Settings

### Access Control
```yaml
# Protected variables
variables:
  API_KEY:
    value: "secret"
    protected: true

# Protected runners
deploy:
  tags:
    - protected
  rules:
    - if: $CI_COMMIT_REF_PROTECTED
```

### Scanning Configuration
```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Dependency-Scanning.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml

sast:
  variables:
    SAST_EXCLUDED_PATHS: "spec, test, tests, tmp"
```

## Integration Setup

### Container Registry
```yaml
build_image:
  image: docker:latest
  services:
    - docker:dind
  variables:
    DOCKER_TLS_CERTDIR: "/certs"
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

### Kubernetes Integration
```yaml
deploy_k8s:
  image: 
    name: bitnami/kubectl:latest
    entrypoint: ['']
  script:
    - kubectl config use-context $KUBE_CONTEXT
    - kubectl apply -f k8s/
  environment:
    name: production
    kubernetes:
      namespace: prod
```

## Monitoring Configuration

### Metrics Configuration
```yaml
# Prometheus metrics
metrics_job:
  script:
    - generate_metrics.sh
  artifacts:
    reports:
      metrics: metrics.txt

# Custom metrics
custom_metrics:
  script:
    - echo "custom_metric{label=\"value\"} 1" > metrics.txt
  artifacts:
    reports:
      metrics: metrics.txt
```

### Logging Setup
```yaml
job_with_logging:
  script:
    - |
      set -x  # Enable debug logging
      echo "Running with debug logs"
  artifacts:
    paths:
      - logs/
    when: always
```

## Performance Optimization

### Job Optimization
```yaml
# Parallel execution
test:
  parallel: 5
  script:
    - npm test

# Matrix jobs
test_matrix:
  parallel:
    matrix:
      - RUBY_VERSION: ['2.7', '3.0', '3.1']
      - DB: ['mysql', 'postgres']
```

### Resource Management
```yaml
job_with_resources:
  tags:
    - high-cpu
  variables:
    CI_RUNNER_MEMORY: 4096
  script:
    - resource_intensive_task.sh
```

## Backup and Recovery

### Configuration Backup
```yaml
# Backup runner configuration
backup_config:
  script:
    - cp /etc/gitlab-runner/config.toml backup/
  artifacts:
    paths:
      - backup/
    expire_in: 1 month
```

### Recovery Procedures
```yaml
# Recovery job
restore_config:
  when: manual
  script:
    - cp backup/config.toml /etc/gitlab-runner/
    - gitlab-runner restart
```
