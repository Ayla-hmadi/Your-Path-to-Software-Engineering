# CircleCI Setup and Configuration Guide

## Initial Setup

### Repository Configuration
```yaml
# Directory structure
project_root/
├── .circleci/
│   └── config.yml
├── src/
└── tests/

# Basic config.yml
version: 2.1
jobs:
  build:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - run: echo "Hello CircleCI"
```

### Project Setup
```yaml
# Project Settings
1. Connect repository
   - Select VCS (GitHub/Bitbucket)
   - Choose repository
   - Authorize CircleCI

2. Configuration options
   - Build on commits
   - Build on pull requests
   - Only build on branches with config
```

## Execution Environment

### Docker Configuration
```yaml
jobs:
  build:
    docker:
      - image: cimg/node:16.0
        auth:
          username: $DOCKER_USER
          password: $DOCKER_PASSWORD
        environment:
          NODE_ENV: test
          
      # Service container
      - image: cimg/postgres:14.0
        environment:
          POSTGRES_USER: user
          POSTGRES_DB: testdb
```

### Machine Executor
```yaml
jobs:
  integration:
    machine:
      image: ubuntu-2004:202010-01
      docker_layer_caching: true
    resource_class: large
    steps:
      - checkout
      - run: docker-compose up -d
```

## Resource Configuration

### Memory and CPU
```yaml
jobs:
  build:
    docker:
      - image: cimg/node:16.0
    resource_class: large  # 4 vCPUs, 8GB RAM
    
  test:
    docker:
      - image: cimg/node:16.0
    resource_class: xlarge  # 8 vCPUs, 16GB RAM
```

### Storage Configuration
```yaml
jobs:
  build:
    docker:
      - image: cimg/node:16.0
    steps:
      - persist_to_workspace:
          root: workspace
          paths:
            - target/

  deploy:
    steps:
      - attach_workspace:
          at: /tmp/workspace
```

## Cache Configuration

### Dependency Caching
```yaml
jobs:
  build:
    steps:
      - restore_cache:
          keys:
            - v1-deps-{{ checksum "package-lock.json" }}
            - v1-deps-
      
      - run: npm install
      
      - save_cache:
          key: v1-deps-{{ checksum "package-lock.json" }}
          paths:
            - node_modules
```

### Custom Caching
```yaml
jobs:
  build:
    steps:
      - restore_cache:
          keys:
            - custom-cache-{{ .Branch }}-{{ .Revision }}
            - custom-cache-{{ .Branch }}
            - custom-cache-
      
      - save_cache:
          key: custom-cache-{{ .Branch }}-{{ .Revision }}
          paths:
            - ~/custom-directory
```

## Environment Setup

### Environment Variables
```yaml
jobs:
  deploy:
    docker:
      - image: cimg/base:stable
    environment:
      AWS_DEFAULT_REGION: us-east-1
      NODE_ENV: production
    steps:
      - run:
          command: |
            echo "Using region $AWS_DEFAULT_REGION"
            echo "Environment is $NODE_ENV"
```

### Contexts
```yaml
workflows:
  version: 2
  deploy:
    jobs:
      - deploy:
          context:
            - aws-credentials
            - production-secrets
```

## Security Configuration

### SSH Keys
```yaml
jobs:
  deploy:
    steps:
      - add_ssh_keys:
          fingerprints:
            - "xx:xx:xx:xx:xx"
      
      - run:
          command: |
            ssh-keyscan -H $DEPLOY_HOST >> ~/.ssh/known_hosts
            scp -r build/* $DEPLOY_USER@$DEPLOY_HOST:/var/www/
```

### Sensitive Data
```yaml
jobs:
  security:
    steps:
      - run:
          name: Security scan
          command: |
            echo $SCAN_TOKEN | security-scanner scan
          environment:
            SCAN_TOKEN: ${SECURITY_SCAN_TOKEN}
```

## Workflow Configuration

### Basic Workflow
```yaml
workflows:
  version: 2
  build-test-deploy:
    jobs:
      - build
      - test:
          requires:
            - build
      - deploy:
          requires:
            - test
          filters:
            branches:
              only: main
```

### Schedule-based Workflows
```yaml
workflows:
  nightly:
    triggers:
      - schedule:
          cron: "0 0 * * *"
          filters:
            branches:
              only:
                - main
    jobs:
      - nightly-build
```

## Integration Setup

### AWS Integration
```yaml
orbs:
  aws-cli: circleci/aws-cli@2.0

jobs:
  deploy:
    executor: aws-cli/default
    steps:
      - aws-cli/setup:
          profile-name: default
      - run:
          command: aws s3 sync ./build s3://my-bucket
```

### Docker Integration
```yaml
orbs:
  docker: circleci/docker@2.1.1

jobs:
  build:
    steps:
      - setup_remote_docker:
          version: 20.10.14
          docker_layer_caching: true
      
      - docker/build:
          image: $DOCKER_IMAGE
          tag: $CIRCLE_SHA1
```

## Monitoring Setup

### Test Results
```yaml
jobs:
  test:
    steps:
      - run:
          command: npm test
          environment:
            JEST_JUNIT_OUTPUT_DIR: ./junit/
            JEST_JUNIT_OUTPUT_NAME: results.xml
      
      - store_test_results:
          path: ./junit/
      
      - store_artifacts:
          path: ./junit/
          destination: test-results
```

### Custom Metrics
```yaml
jobs:
  performance:
    steps:
      - run:
          name: Performance tests
          command: |
            ./performance-test.sh > metrics.txt
      
      - store_artifacts:
          path: metrics.txt
          destination: performance-metrics
```

## Backup Configuration

### Workspace Backup
```yaml
jobs:
  backup:
    steps:
      - persist_to_workspace:
          root: .
          paths:
            - .circleci/
            - scripts/
            - config/
      
      - store_artifacts:
          path: backup/
          destination: config-backup
```

### Configuration Backup
```yaml
jobs:
  export_config:
    steps:
      - run:
          name: Export configuration
          command: |
            circleci config pack src/ > packed_config.yml
      
      - store_artifacts:
          path: packed_config.yml
          destination: circleci-config
```
