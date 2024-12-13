# CI/CD Glossary of Terms

## A

### Artifacts
Binary or non-binary files generated during the build/deployment process (e.g., compiled code, documentation, reports).

### Automated Testing
Process of executing pre-written tests automatically as part of the CI/CD pipeline.

### Agent
A machine or container that executes CI/CD pipeline jobs.

## B

### Build
Process of converting source code into a standalone software artifact.

### Build Agent
Server or container responsible for executing build processes.

### Blue-Green Deployment
Deployment strategy using two identical environments for zero-downtime releases.

## C

### Continuous Integration (CI)
Practice of automatically integrating code changes from multiple contributors into a single software project.

### Continuous Delivery (CD)
Extension of CI to automate the delivery of applications to selected environments.

### Continuous Deployment (CD)
Automated deployment of every change that passes the pipeline directly to production.

### Configuration as Code
Practice of managing configuration files in version control and treating them as code.

## D

### Deployment Pipeline
Automated manifestation of the process for getting software from version control to users.

### Dependencies
External resources, libraries, or packages required by an application.

### DevOps
Set of practices combining software development and IT operations to shorten development cycles.

## E

### Environment
A set of infrastructure and configurations where applications run (e.g., development, staging, production).

### End-to-End Testing
Testing approach that verifies the flow of an application from start to finish.

## F

### Feature Flag
Configuration that enables/disables features in an application without deploying new code.

### Feedback Loop
Process of collecting and acting on results from various pipeline stages.

## G

### Git
Distributed version control system for tracking changes in source code.

### Gate
Checkpoint in the pipeline that must be passed before proceeding to the next stage.

## I

### Infrastructure as Code (IaC)
Managing and provisioning infrastructure through code instead of manual processes.

### Integration Testing
Testing phase where individual modules are combined and tested as a group.

## J

### Job
Single unit of work in a CI/CD pipeline (e.g., build, test, deploy).

## K

### Kubernetes
Container orchestration platform often used in CI/CD deployments.

## M

### Microservices
Architectural style structuring an application as a collection of loosely coupled services.

### Monitoring
Continuous observation of application and infrastructure health and performance.

## P

### Pipeline
Automated sequence of processes for delivering software from version control to production.

### Pull Request
Proposed changes to a repository submitted by a developer for review.

## R

### Regression Testing
Testing to ensure that previously developed features still perform after new changes.

### Rolling Deployment
Deployment strategy that gradually replaces instances of the previous version with the new version.

### Repository
Storage location for version-controlled code and related files.

## S

### Source Control
Management of changes to documents, programs, and other information stored as computer files.

### Stage
Logical division in a CI/CD pipeline (e.g., build, test, deploy).

### Static Code Analysis
Analysis of code without executing it, often for quality and security purposes.

## T

### Test Coverage
Measure of how much of the code is tested by the test suite.

### Test Environment
Isolated environment where testing is performed without affecting production.

### Trigger
Event that initiates a pipeline execution (e.g., code commit, manual action).

## U

### Unit Testing
Testing of individual components or functions in isolation.

## V

### Version Control
System that records changes to files over time.

### Virtual Machine
Emulation of a computer system, often used in CI/CD environments.

## W

### Webhook
Automated message sent from applications when something occurs.

### Workflow
Sequence of steps that define how work should proceed.

## Advanced Terms

### Canary Deployment
```yaml
Definition: Deployment strategy where changes are rolled out to a small subset of users before full deployment
Usage: Risk mitigation and gradual feature rollout
Related: Feature flags, A/B testing
```

### Artifact Repository
```yaml
Definition: Storage for build outputs, dependencies, and deployable applications
Examples: 
  - JFrog Artifactory
  - Nexus Repository
  - Docker Registry
Purpose: Version control for binaries and dependencies
```

### Quality Gate
```yaml
Definition: Set of criteria that must be met before a pipeline can proceed
Criteria Examples:
  - Code coverage threshold
  - Security scan results
  - Performance metrics
  - Compliance checks
```

### Service Mesh
```yaml
Definition: Infrastructure layer for handling service-to-service communication
Components:
  - Service discovery
  - Load balancing
  - Failure recovery
  - Metrics collection
```
