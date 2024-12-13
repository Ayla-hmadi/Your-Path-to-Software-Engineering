# DevOps Cheat Sheets

## Git Commands

### 1. Basic Operations
```bash
# Repository setup
git init                    # Initialize repository
git clone <url>            # Clone repository
git remote add origin <url> # Add remote

# Basic workflow
git add <file>             # Stage changes
git commit -m "message"    # Commit changes
git push origin <branch>   # Push changes
git pull origin <branch>   # Pull changes

# Branch operations
git branch                 # List branches
git branch <name>          # Create branch
git checkout <branch>      # Switch branch
git merge <branch>         # Merge branch

# Status and history
git status                 # Check status
git log                    # View history
git diff                   # View changes
```

### 2. Advanced Git
```bash
# Stash operations
git stash                  # Stash changes
git stash list            # List stashes
git stash pop             # Apply and remove stash
git stash apply           # Apply stash

# History modification
git rebase -i HEAD~3      # Interactive rebase
git commit --amend        # Modify last commit
git reset --hard HEAD^    # Undo last commit
git cherry-pick <commit>  # Copy commit to current branch
```

## Docker Commands

### 1. Container Management
```bash
# Container operations
docker run <image>         # Run container
docker ps                  # List containers
docker stop <container>    # Stop container
docker rm <container>      # Remove container
docker exec -it <container> # Execute in container

# Image operations
docker images             # List images
docker pull <image>       # Pull image
docker build -t <name> .  # Build image
docker push <image>       # Push image
```

### 2. Docker Compose
```yaml
# docker-compose.yml example
version: '3'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: secret
```

## Kubernetes Commands

### 1. Pod Management
```bash
# Pod operations
kubectl get pods          # List pods
kubectl describe pod <pod> # Pod details
kubectl logs <pod>        # View logs
kubectl exec -it <pod>    # Execute in pod
kubectl delete pod <pod>  # Delete pod

# Deployment operations
kubectl create -f <file>  # Create resource
kubectl apply -f <file>   # Apply changes
kubectl scale deployment  # Scale deployment
kubectl rollout status    # Check rollout
```

### 2. Configuration
```yaml
# Pod configuration example
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80

# Service configuration
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```

## Jenkins Pipeline

### 1. Jenkinsfile Syntax
```groovy
// Basic pipeline
pipeline {
    agent any
    stages {
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

// Advanced pipeline
pipeline {
    agent {
        docker {
            image 'maven:3.8.1-jdk-11'
        }
    }
    environment {
        MAVEN_OPTS = '-Xmx3200m'
    }
    stages {
        stage('Build') {
            when {
                branch 'master'
            }
            steps {
                sh 'mvn -B verify'
            }
        }
    }
}
```

## Terraform Commands

### 1. Basic Operations
```bash
# Initialization and planning
terraform init            # Initialize directory
terraform plan            # Create execution plan
terraform apply          # Apply changes
terraform destroy        # Destroy resources

# State management
terraform state list     # List resources
terraform import         # Import resource
terraform state mv      # Move resource
terraform state rm      # Remove resource
```

### 2. Configuration
```hcl
# Provider configuration
provider "aws" {
  region = "us-west-2"
}

# Resource example
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
  
  tags = {
    Name = "web-server"
  }
}
```

## Linux Commands

### 1. System Operations
```bash
# File operations
ls -la                   # List files
cp -r source dest        # Copy recursively
mv source dest           # Move/rename
rm -rf directory        # Remove recursively

# Process management
ps aux                  # List processes
top                     # Process viewer
kill -9 <pid>           # Kill process
systemctl status <service> # Service status
```

### 2. Network Operations
```bash
# Network commands
netstat -tulpn          # List ports
curl -X GET <url>       # HTTP request
wget <url>              # Download file
ssh user@host           # SSH connection
scp file user@host:path # Secure copy
```

## AWS CLI

### 1. Basic Commands
```bash
# EC2 operations
aws ec2 describe-instances
aws ec2 start-instances --instance-ids <id>
aws ec2 stop-instances --instance-ids <id>

# S3 operations
aws s3 ls
aws s3 cp file s3://bucket/
aws s3 sync source s3://bucket/
```

### 2. Configuration
```bash
# Configure AWS CLI
aws configure
AWS Access Key ID [None]: YOUR_ACCESS_KEY
AWS Secret Access Key [None]: YOUR_SECRET_KEY
Default region name [None]: us-west-2
Default output format [None]: json
```

## Monitoring Commands

### 1. Prometheus Queries
```promql
# Basic queries
rate(http_requests_total[5m])
sum by (status_code) (http_requests_total)
max_over_time(process_cpu_seconds_total[1h])

# Alert rules
alert: HighErrorRate
expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.5
for: 10m
```

### 2. Grafana
```yaml
# Dashboard JSON example
{
  "panels": [
    {
      "title": "Request Rate",
      "type": "graph",
      "datasource": "Prometheus",
      "targets": [
        {
          "expr": "rate(http_requests_total[5m])"
        }
      ]
    }
  ]
}
```

## Conclusion

These cheat sheets provide quick reference for common DevOps operations. Regular practice and reference will help memorize these commands and patterns.

## Quick Reference Tips
1. Keep commands organized by category
2. Practice regularly
3. Understand command options
4. Create custom aliases
5. Maintain personal notes
6. Update regularly
