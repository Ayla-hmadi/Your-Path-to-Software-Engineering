# Jenkins Setup and Configuration Guide

## Installation

### System Requirements
```yaml
Minimum Requirements:
  CPU: 2 cores
  Memory: 4GB RAM
  Disk: 50GB
  Java: OpenJDK 11 or 17

Recommended:
  CPU: 4+ cores
  Memory: 8GB+ RAM
  Disk: 100GB+ SSD
  Java: OpenJDK 17
```

### Installation Methods

#### Docker Installation
```bash
# Pull Jenkins Image
docker pull jenkins/jenkins:lts

# Run Jenkins Container
docker run -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name jenkins \
  jenkins/jenkins:lts
```

#### Native Installation
```bash
# Ubuntu/Debian
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins

# RHEL/CentOS
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins
```

## Initial Setup

### First-Time Configuration
```yaml
Steps:
  1. Access Jenkins URL (default: http://localhost:8080)
  2. Retrieve initial admin password
  3. Install suggested plugins
  4. Create admin user
  5. Configure instance URL
```

### Security Configuration
```yaml
Basic Security:
  - Enable security
  - Configure authentication
  - Set up authorization
  - Configure CSRF protection
  - Enable agent-to-master security
```

## System Configuration

### Global Tools
```yaml
Tool Configuration:
  JDK:
    - Name: JDK17
    - Installation directory
    - Auto installation

Maven:
    - Name: Maven 3.8
    - Installation directory
    - Auto installation

Git:
    - Name: Default Git
    - Path to git executable
```

### System Settings
```yaml
Jenkins Location:
  - Jenkins URL
  - System Admin email

Global Properties:
  - Environment variables
  - Tool locations
  - Path variables

Workspace Settings:
  - Workspace root directory
  - Build record directory
```

## Plugin Management

### Essential Plugins
```yaml
Core Plugins:
  - Git plugin
  - Pipeline plugins
  - Credentials plugin
  - Docker plugin
  - Build triggers

Additional Plugins:
  - Blue Ocean
  - Role-based Authorization
  - Pipeline AWS Steps
  - SonarQube Scanner
```

### Plugin Configuration
```yaml
Installation:
  - Via UI: Manage Jenkins > Plugins
  - Via CLI: jenkins-plugin-cli
  - Via Configuration as Code

Management:
  - Version control
  - Dependency resolution
  - Update strategy
  - Security patches
```

## Agent Configuration

### Agent Types
```yaml
Permanent Agents:
  - Direct connection
  - SSH connection
  - JNLP connection

Cloud Agents:
  - Docker
  - Kubernetes
  - AWS EC2
  - Azure VMs
```

### Agent Setup
```groovy
// Jenkins Pipeline Agent Configuration
pipeline {
    agent {
        docker {
            image 'maven:3.8.4-openjdk-17'
            args '-v $HOME/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
```

## Credential Management

### Credential Types
```yaml
Supported Types:
  - Username/Password
  - SSH Keys
  - Secret Files
  - Secret Text
  - Certificates
```

### Credential Scope
```yaml
Scope Levels:
  - System: Available to Jenkins and jobs
  - Global: Available to all items
  - Project: Limited to specific projects
```

## Pipeline Configuration

### Basic Pipeline
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/user/repo.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
```

### Shared Libraries
```groovy
// In Jenkinsfile
@Library('my-shared-library') _

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                mySharedFunction()
            }
        }
    }
}
```

## Backup Configuration

### Backup Strategy
```yaml
Elements to Backup:
  - JENKINS_HOME directory
  - Configuration files
  - Job configurations
  - Build history
  - Plugins directory

Backup Methods:
  - File system backup
  - ThinBackup plugin
  - Periodic snapshots
```

### Disaster Recovery
```yaml
Recovery Steps:
  1. Stop Jenkins
  2. Restore JENKINS_HOME
  3. Verify permissions
  4. Start Jenkins
  5. Verify functionality
```

## Monitoring Setup

### System Monitoring
```yaml
Metrics to Monitor:
  - CPU usage
  - Memory consumption
  - Disk space
  - Build queue
  - Build duration

Tools:
  - Metrics plugin
  - Prometheus
  - Grafana
```

### Logging Configuration
```yaml
Log Settings:
  - Log rotation
  - Log levels
  - Audit logging
  - System logging
  - Build logging
```

## Security Hardening

### Security Measures
```yaml
Recommended Settings:
  - Enable CSRF protection
  - Configure proxy settings
  - Enable agent-master access control
  - Configure script security
  - Enable audit logging
```

### Access Control
```yaml
Configuration:
  - Matrix-based security
  - Project-based security
  - Role-based access
  - API token management
  - LDAP integration
```
