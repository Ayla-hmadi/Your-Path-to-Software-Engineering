# Jenkins Pipeline Scripts Guide

## Pipeline Types

### Declarative Pipeline
```groovy
pipeline {
    agent any
    environment {
        MAVEN_OPTS = '-Xmx3072m'
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
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

### Scripted Pipeline
```groovy
node {
    try {
        stage('Checkout') {
            checkout scm
        }
        stage('Build') {
            sh 'mvn clean install'
        }
    } catch (e) {
        throw e
    } finally {
        cleanWs()
    }
}
```

## Pipeline Components

### Agents
```groovy
// Single Agent
agent any

// Docker Agent
agent {
    docker {
        image 'maven:3.8.4-openjdk-17'
        args '-v $HOME/.m2:/root/.m2'
    }
}

// Kubernetes Agent
agent {
    kubernetes {
        yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: maven
                image: maven:3.8.4-openjdk-17
                command:
                - cat
                tty: true
        '''
    }
}
```

### Environment Variables
```groovy
environment {
    // Global variables
    DEPLOY_ENV = 'production'
    
    // Credentials
    AWS_CREDS = credentials('aws-credentials')
    
    // Dynamic variables
    VERSION = sh(script: 'git describe --tags', returnStdout: true).trim()
}

// Stage-level environment
stage('Deploy') {
    environment {
        STAGE_VAR = 'stage-specific'
    }
    steps {
        sh 'echo $STAGE_VAR'
    }
}
```

## Pipeline Stages

### Build Stages
```groovy
stages {
    stage('Compile') {
        steps {
            sh 'mvn compile'
        }
    }
    
    stage('Unit Test') {
        steps {
            sh 'mvn test'
        }
        post {
            always {
                junit '**/target/surefire-reports/*.xml'
            }
        }
    }
    
    stage('Package') {
        steps {
            sh 'mvn package'
            archiveArtifacts artifacts: 'target/*.jar'
        }
    }
}
```

### Parallel Stages
```groovy
stage('Test') {
    parallel {
        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Integration Tests') {
            steps {
                sh 'mvn verify'
            }
        }
        stage('Security Scan') {
            steps {
                sh './security-scan.sh'
            }
        }
    }
}
```

## Conditional Execution

### When Conditions
```groovy
stage('Deploy to Production') {
    when {
        branch 'main'
        environment name: 'DEPLOY_TO', value: 'production'
        expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
    }
    steps {
        sh './deploy.sh production'
    }
}
```

### Input Steps
```groovy
stage('Deploy') {
    steps {
        input message: 'Deploy to production?',
              parameters: [
                  choice(name: 'ENV', choices: ['staging', 'production'], description: 'Select environment')
              ]
        
        sh "./deploy.sh ${ENV}"
    }
}
```

## Error Handling

### Try-Catch Blocks
```groovy
stage('Deploy') {
    steps {
        script {
            try {
                sh './deploy.sh'
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                error("Deployment failed: ${e.getMessage()}")
            } finally {
                sh './cleanup.sh'
            }
        }
    }
}
```

### Post Actions
```groovy
post {
    always {
        cleanWs()
    }
    success {
        slackSend channel: '#deploy',
                  color: 'good',
                  message: "Build successful: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
    }
    failure {
        emailext subject: "Build Failed: ${env.JOB_NAME}",
                 body: "Check console output at ${env.BUILD_URL}",
                 to: 'team@example.com'
    }
}
```

## Shared Libraries

### Library Structure
```groovy
// vars/buildJava.groovy
def call(Map config) {
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    sh "mvn ${config.mavenGoals ?: 'clean package'}"
                }
            }
        }
    }
}

// Usage in Jenkinsfile
@Library('my-shared-library') _
buildJava mavenGoals: 'clean install'
```

### Custom Steps
```groovy
// vars/notifySlack.groovy
def call(String message, String channel = '#general') {
    slackSend channel: channel,
              color: 'good',
              message: message
}

// Usage
notifySlack 'Deployment successful!', '#deployments'
```

## Advanced Techniques

### Matrix Builds
```groovy
matrix {
    axes {
        axis {
            name 'PLATFORM'
            values 'linux', 'windows', 'mac'
        }
        axis {
            name 'BROWSER'
            values 'chrome', 'firefox', 'safari'
        }
    }
    stages {
        stage('Test') {
            steps {
                sh "./test.sh ${PLATFORM} ${BROWSER}"
            }
        }
    }
}
```

### Docker Pipeline
```groovy
pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                dockerfile {
                    filename 'Dockerfile.build'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn package'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'testimage:latest'
                    reuseNode true
                }
            }
            steps {
                sh './run-tests.sh'
            }
        }
    }
}
```

## Best Practices

### Code Organization
```groovy
// Modular stages
def buildStage() {
    stage('Build') {
        sh 'mvn clean package'
    }
}

def testStage() {
    stage('Test') {
        sh 'mvn test'
    }
}

// Main pipeline
pipeline {
    agent any
    stages {
        stage('Build and Test') {
            steps {
                script {
                    buildStage()
                    testStage()
                }
            }
        }
    }
}
```

### Pipeline Templates
```groovy
// Template for Java projects
def call(Map config) {
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    sh "${config.buildCommand ?: 'mvn clean package'}"
                }
            }
            stage('Test') {
                steps {
                    sh "${config.testCommand ?: 'mvn test'}"
                }
            }
            stage('Deploy') {
                when { expression { config.deploy } }
                steps {
                    sh "${config.deployCommand}"
                }
            }
        }
    }
}
```
