# CircleCI Best Practices Guide

## Configuration Best Practices

### YAML Structure
```yaml
version: 2.1

# Use orbs for common functionality
orbs:
  node: circleci/node@4.7
  docker: circleci/docker@2.1.1

# Define reusable commands
commands:
  install_dependencies:
    steps:
      - restore_cache:
          key: deps-{{ checksum "package-lock.json" }}
      - run: npm install
      - save_cache:
          key: deps-{{ checksum "package-lock.json" }}
          paths:
            - node_modules

# Define job templates
jobs:
  build:
    docker:
      - image: cimg/node:16.0
    steps:
      - checkout
      - install_dependencies
      - run: npm run build
```

### Workflow Organization
```yaml
workflows:
  version: 2
  build-test-deploy:
    jobs:
      # Organize jobs in logical order
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

## Performance Optimization

### Caching Strategy
```yaml
jobs:
  build:
    steps:
      # Use multiple fallback keys
      - restore_cache:
          keys:
            - v1-deps-{{ .Branch }}-{{ checksum "package-lock.json" }}
            - v1-deps-{{ .Branch }}
            - v1-deps

      # Save with specific key
      - save_cache:
          key: v1-deps-{{ .Branch }}-{{ checksum "package-lock.json" }}
          paths:
            - node_modules
            - ~/.npm
            - ~/.cache
```

### Parallel Execution
```yaml
jobs:
  test:
    parallelism: 4
    steps:
      - checkout
      - run:
          command: |
            TESTFILES=$(circleci tests glob "test/**/*.test.js" | circleci tests split)
            npm test $TESTFILES
            
  integration:
    parallelism: 2
    steps:
      - run:
          command: |
            SPECS=$(find specs -name '*_spec.rb' | circleci tests split)
            bundle exec rspec $SPECS
```

## Resource Management

### Workspace Optimization
```yaml
jobs:
  build:
    steps:
      # Be selective with workspace persistence
      - persist_to_workspace:
          root: .
          paths:
            - dist/
            - package.json
            - configs/

  deploy:
    steps:
      # Attach workspace at specific location
      - attach_workspace:
          at: ./project
```

### Docker Layer Caching
```yaml
jobs:
  build:
    steps:
      - setup_remote_docker:
          version: 20.10.14
          docker_layer_caching: true
      
      # Use multi-stage builds
      - run:
          command: |
            docker build \
              --cache-from app:latest \
              -t app:$CIRCLE_SHA1 .
```

## Security Practices

### Secret Management
```yaml
# Use contexts for sensitive data
workflows:
  deploy:
    jobs:
      - deploy:
          context:
            - production-secrets
            - aws-credentials

jobs:
  deploy:
    steps:
      # Avoid printing secrets
      - run:
          command: |
            echo "Using encrypted values"
            aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
```

### Access Control
```yaml
jobs:
  deploy:
    # Use restricted resource classes
    resource_class: medium
    steps:
      # Implement least privilege
      - aws-cli/setup:
          profile-name: deploy-only
      - run:
          command: |
            aws s3 sync ./dist s3://my-bucket/
```

## Testing Strategy

### Test Organization
```yaml
jobs:
  test:
    steps:
      # Organize tests by type
      - run:
          name: Unit Tests
          command: npm run test:unit
          
      - run:
          name: Integration Tests
          command: npm run test:integration
          
      - run:
          name: E2E Tests
          command: npm run test:e2e
          
      # Store test results
      - store_test_results:
          path: test-results
```

### Test Splitting
```yaml
jobs:
  test:
    parallelism: 4
    steps:
      - run:
          command: |
            # Split by timing data
            TESTFILES=$(circleci tests glob "test/**/*.test.js" | \
              circleci tests split --split-by=timings)
            npm test $TESTFILES
```

## Error Handling

### Job Failure Handling
```yaml
jobs:
  deploy:
    steps:
      - run:
          name: Deploy Application
          command: |
            ./deploy.sh
          # Handle failures
          when: on_fail
          command: |
            ./rollback.sh
            ./notify-team.sh

      # Cleanup regardless of success/failure
      - run:
          when: always
          command: ./cleanup.sh
```

### Retry Strategy
```yaml
jobs:
  deploy:
    steps:
      - run:
          name: Deploy with retries
          command: |
            for i in {1..3}; do
              if ./deploy.sh; then
                exit 0
              fi
              sleep 5
            done
            exit 1
```

## Monitoring and Logging

### Log Management
```yaml
jobs:
  build:
    steps:
      - run:
          name: Build with logs
          command: |
            set -x  # Enable debug logging
            npm run build 2>&1 | tee build.log
            
      # Store logs as artifacts
      - store_artifacts:
          path: build.log
          destination: build-logs
```

### Performance Monitoring
```yaml
jobs:
  performance:
    steps:
      - run:
          name: Performance tests
          command: |
            ./performance-test.sh > metrics.txt
            
      # Store metrics for analysis
      - store_artifacts:
          path: metrics.txt
          destination: performance-metrics
```

## Documentation

### Code Comments
```yaml
version: 2.1

# Define reusable executor
executors:
  node-docker:
    docker:
      - image: cimg/node:16.0
    # Specify resource allocation
    resource_class: large

# Define reusable command
commands:
  install_deps:
    description: "Install and cache dependencies"
    steps:
      - restore_cache:
          keys:
            - deps-{{ checksum "package.json" }}
```

### Workflow Documentation
```yaml
# Document complex workflows
workflows:
  version: 2
  build-test-deploy:
    jobs:
      # Development builds
      - build-dev:
          filters:
            branches:
              ignore: main
              
      # Production deployment
      - build-prod:
          filters:
            branches:
              only: main
```
