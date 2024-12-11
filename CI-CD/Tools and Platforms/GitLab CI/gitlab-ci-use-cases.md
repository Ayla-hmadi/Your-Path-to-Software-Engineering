# GitLab CI Common Use Cases

## Node.js Application

### Basic Node.js Pipeline
```yaml
image: node:16

stages:
  - install
  - test
  - build
  - deploy

cache:
  paths:
    - node_modules/

install_dependencies:
  stage: install
  script:
    - npm ci
  artifacts:
    paths:
      - node_modules/

test:
  stage: test
  script:
    - npm run test
  coverage: '/Coverage: \d+.\d+%/'
  artifacts:
    reports:
      junit: junit.xml
      coverage: coverage/lcov.info

build:
  stage: build
  script:
    - npm run build
  artifacts:
    paths:
      - dist/

deploy:
  stage: deploy
  script:
    - npm run deploy
  environment:
    name: production
    url: https://example.com
  only:
    - main
```

## Java Spring Boot Application

### Maven Build Pipeline
```yaml
image: maven:3.8.4-openjdk-17

variables:
  MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"

cache:
  paths:
    - .m2/repository/

stages:
  - test
  - build
  - package
  - deploy

test:
  stage: test
  script:
    - mvn test
  artifacts:
    reports:
      junit:
        - target/surefire-reports/TEST-*.xml

build:
  stage: build
  script:
    - mvn clean package -DskipTests
  artifacts:
    paths:
      - target/*.jar

docker_build:
  stage: package
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

deploy:
  stage: deploy
  script:
    - kubectl set image deployment/app app=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

## Python Django Application

### Django Pipeline
```yaml
image: python:3.9

variables:
  PIP_CACHE_DIR: "$CI_PROJECT_DIR/.pip-cache"

cache:
  paths:
    - .pip-cache/
    - venv/

before_script:
  - python -m venv venv
  - source venv/bin/activate
  - pip install -r requirements.txt

stages:
  - test
  - build
  - deploy

test:
  stage: test
  script:
    - python manage.py test
    - coverage run manage.py test
    - coverage report
  coverage: '/TOTAL.+ ([0-9]{1,3}%)/'

build:
  stage: build
  script:
    - python manage.py collectstatic --noinput
  artifacts:
    paths:
      - staticfiles/

deploy:
  stage: deploy
  script:
    - echo "Deploying to production"
    - gunicorn myapp.wsgi:application
```

## Docker Container Build

### Multi-Stage Docker Build
```yaml
stages:
  - build
  - test
  - push
  - deploy

variables:
  DOCKER_TLS_CERTDIR: "/certs"

build:
  image: docker:latest
  services:
    - docker:dind
  stage: build
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

test:
  image: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  stage: test
  script:
    - ./run_tests.sh

push_latest:
  image: docker:latest
  services:
    - docker:dind
  stage: push
  script:
    - docker pull $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - docker tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA $CI_REGISTRY_IMAGE:latest
    - docker push $CI_REGISTRY_IMAGE:latest
  only:
    - main
```

## Kubernetes Deployment

### K8s Pipeline
```yaml
stages:
  - build
  - test
  - deploy

variables:
  KUBE_CONTEXT: production

.kube_config: &kube_config
  before_script:
    - kubectl config use-context $KUBE_CONTEXT

deploy_staging:
  <<: *kube_config
  stage: deploy
  script:
    - kubectl apply -f k8s/staging/
  environment:
    name: staging
    kubernetes:
      namespace: staging
  only:
    - develop

deploy_production:
  <<: *kube_config
  stage: deploy
  script:
    - kubectl apply -f k8s/production/
  environment:
    name: production
    kubernetes:
      namespace: production
  when: manual
  only:
    - main
```

## React Frontend Application

### React Build and Deploy
```yaml
image: node:16

stages:
  - install
  - test
  - build
  - deploy

variables:
  PUBLIC_URL: https://example.com

cache:
  paths:
    - node_modules/
    - .yarn-cache/

install:
  stage: install
  script:
    - yarn install --cache-folder .yarn-cache
  artifacts:
    paths:
      - node_modules/

test:
  stage: test
  script:
    - yarn test --coverage
  coverage: '/All files[^|]*\|[^|]*\s+([\d\.]+)/'

build:
  stage: build
  script:
    - yarn build
  artifacts:
    paths:
      - build/

pages:
  stage: deploy
  script:
    - mv build public
  artifacts:
    paths:
      - public
  only:
    - main
```

## Database Migrations

### Database Update Pipeline
```yaml
image: postgres:13

variables:
  POSTGRES_DB: myapp
  POSTGRES_USER: runner
  POSTGRES_PASSWORD: secret

stages:
  - backup
  - migrate
  - verify

database_backup:
  stage: backup
  script:
    - pg_dump $DATABASE_URL > backup.sql
  artifacts:
    paths:
      - backup.sql

run_migrations:
  stage: migrate
  script:
    - psql $DATABASE_URL -f migrations/*.sql
  dependencies:
    - database_backup

verify_migration:
  stage: verify
  script:
    - psql $DATABASE_URL -c "SELECT version FROM schema_version;"
```

## Microservices Deployment

### Multi-Service Pipeline
```yaml
stages:
  - build
  - test
  - deploy

.service_template: &service_template
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY

build_auth_service:
  <<: *service_template
  stage: build
  script:
    - cd auth-service
    - docker build -t $CI_REGISTRY_IMAGE/auth:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE/auth:$CI_COMMIT_SHA

build_api_service:
  <<: *service_template
  stage: build
  script:
    - cd api-service
    - docker build -t $CI_REGISTRY_IMAGE/api:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE/api:$CI_COMMIT_SHA

deploy_services:
  stage: deploy
  script:
    - kubectl set image deployment/auth auth=$CI_REGISTRY_IMAGE/auth:$CI_COMMIT_SHA
    - kubectl set image deployment/api api=$CI_REGISTRY_IMAGE/api:$CI_COMMIT_SHA
  environment:
    name: production
```
