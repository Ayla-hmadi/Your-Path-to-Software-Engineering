# Introduction to CircleCI

## Overview
CircleCI is a continuous integration and continuous delivery platform that automates the build, test, and deployment processes. It specializes in providing fast, reliable, and secure automation for modern software development.

## Core Concepts

### Configuration Structure
```yaml
version: 2.1

orbs:
  node: circleci/node@4.7
  docker: circleci/docker@2.1.1

jobs:
  build:
    docker:
      - image: cimg/node:16.0
    steps:
      - checkout
      - node/install-packages
      - run: npm test

workflows:
  version: 2
  build-test-deploy:
    jobs:
      - build
```

### Basic Components

#### Jobs
```yaml
jobs:
  build:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - run:
          name: Build Application
          command: make build

  test:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - run:
          name: Run Tests
          command: make test
```

#### Workflows
```yaml
workflows:
  version: 2
  build_and_test:
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

## Key Features

### Orbs
```yaml
orbs:
  aws-s3: circleci/aws-s3@3.0
  kubernetes: circleci/kubernetes@1.0
  docker: circleci/docker@2.1.1

jobs:
  deploy:
    docker:
      - image: cimg/python:3.9
    steps:
      - aws-s3/copy:
          from: bucket/build
          to: 's3://production-bucket'
```

### Parallelism
```yaml
jobs:
  test:
    parallelism: 4
    steps:
      - checkout
      - run:
          command: |
            TEST_FILES=$(circleci tests glob "test/**/*.test.js" | circleci tests split)
            npm test $TEST_FILES
```

## Execution Environment

### Docker Execution
```yaml
jobs:
  build:
    docker:
      - image: cimg/node:16.0
        auth:
          username: $DOCKERHUB_USERNAME
          password: $DOCKERHUB_PASSWORD
      - image: cimg/postgres:14.0
        environment:
          POSTGRES_USER: circleci
          POSTGRES_DB: circle_test
```

### Machine Execution
```yaml
jobs:
  build:
    machine:
      image: ubuntu-2004:202010-01
    steps:
      - checkout
      - run:
          name: Run integration tests
          command: make integration-tests
```

## Resource Management

### Caching
```yaml
jobs:
  build:
    steps:
      - restore_cache:
          keys:
            - v1-deps-{{ .Branch }}-{{ checksum "package-lock.json" }}
            - v1-deps-{{ .Branch }}
            - v1-deps

      - save_cache:
          key: v1-deps-{{ .Branch }}-{{ checksum "package-lock.json" }}
          paths:
            - node_modules
```

### Artifacts
```yaml
jobs:
  build:
    steps:
      - run:
          name: Build assets
          command: npm run build
      - store_artifacts:
          path: build
          destination: build-output
      - store_test_results:
          path: test-results
```

## Pipeline Optimization

### Resource Classes
```yaml
jobs:
  build:
    docker:
      - image: cimg/node:16.0
    resource_class: large
    steps:
      - checkout
      - run: npm run build
```

### Test Splitting
```yaml
jobs:
  test:
    parallelism: 4
    steps:
      - run:
          command: |
            TESTS=$(circleci tests glob "spec/**/*_spec.rb" | circleci tests split)
            bundle exec rspec $TESTS
```

## Security Features

### Contexts
```yaml
workflows:
  deploy:
    jobs:
      - deploy:
          context: 
            - aws-credentials
            - production-deploy
```

### Environment Variables
```yaml
jobs:
  deploy:
    docker:
      - image: cimg/python:3.9
    environment:
      AWS_REGION: us-east-1
    steps:
      - run:
          command: aws s3 sync . s3://mybucket
```

## Best Practices

### Job Organization
```yaml
jobs:
  lint:
    steps:
      - run: npm run lint
  
  test:
    steps:
      - run: npm test
  
  build:
    steps:
      - run: npm run build
```

### Workflow Design
```yaml
workflows:
  version: 2
  build-test-deploy:
    jobs:
      - lint
      - test:
          requires:
            - lint
      - build:
          requires:
            - test
      - deploy:
          requires:
            - build
          filters:
            branches:
              only: main
```

## Common Patterns

### Matrix Jobs
```yaml
workflows:
  test-matrix:
    jobs:
      - test:
          matrix:
            parameters:
              node-version: ["14.17", "16.13", "18.0"]
              os: [linux, windows]
```

### Approval Jobs
```yaml
workflows:
  deploy:
    jobs:
      - build
      - hold:
          type: approval
          requires:
            - build
      - deploy:
          requires:
            - hold
```

## Troubleshooting

### Debug Tools
```yaml
jobs:
  build:
    steps:
      - run:
          name: Debug Information
          command: |
            echo "Current directory: $PWD"
            echo "List files: $(ls -la)"
            echo "Environment: $CIRCLE_BRANCH"
```

### SSH Access
```yaml
jobs:
  build:
    steps:
      - add_ssh_keys:
          fingerprints:
            - "xx:xx:xx:xx:xx"
      - run:
          command: |
            ssh $SSH_USER@$SSH_HOST "./deploy.sh"
```
