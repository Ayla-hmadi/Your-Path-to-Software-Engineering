# Scripting Languages in DevOps

## Introduction

This guide covers the most commonly used scripting languages in DevOps, their applications, and best practices for implementation in automation and infrastructure management.

## Shell Scripting (Bash)

### 1. Basic Syntax
```bash
#!/bin/bash

# Variables
NAME="DevOps"
echo "Hello, $NAME!"

# Functions
function backup_files() {
    local source_dir=$1
    local backup_dir=$2
    tar -czf "$backup_dir/backup-$(date +%Y%m%d).tar.gz" "$source_dir"
}

# Control structures
if [ -d "$directory" ]; then
    echo "Directory exists"
else
    mkdir -p "$directory"
fi
```

### 2. Common Use Cases
```bash
# System monitoring
#!/bin/bash

# Monitor system resources
while true; do
    echo "Memory Usage:"
    free -h
    echo "CPU Usage:"
    top -bn1 | grep "Cpu(s)"
    echo "Disk Usage:"
    df -h
    sleep 60
done

# Log rotation
#!/bin/bash

LOG_DIR="/var/log/app"
MAX_LOGS=5

cd $LOG_DIR
ls -t | grep "\.log$" | tail -n +$((MAX_LOGS + 1)) | xargs rm -f
```

## Python

### 1. Basic Syntax
```python
#!/usr/bin/env python3

import os
import sys
import logging

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def process_files(directory):
    """Process files in the specified directory."""
    try:
        for filename in os.listdir(directory):
            filepath = os.path.join(directory, filename)
            if os.path.isfile(filepath):
                logger.info(f"Processing {filename}")
                # Process file
    except Exception as e:
        logger.error(f"Error processing files: {e}")
        sys.exit(1)

if __name__ == "__main__":
    process_files(sys.argv[1])
```

### 2. DevOps Applications
```python
# AWS Resource Management
import boto3

def create_ec2_instance():
    ec2 = boto3.resource('ec2')
    instance = ec2.create_instances(
        ImageId='ami-12345678',
        MinCount=1,
        MaxCount=1,
        InstanceType='t2.micro',
        KeyName='my-key-pair'
    )
    return instance[0].id

# Docker Container Management
import docker

def manage_containers():
    client = docker.from_client()
    containers = client.containers.list()
    for container in containers:
        print(f"Container {container.name}: {container.status}")
```

## PowerShell

### 1. Basic Syntax
```powershell
# Functions and Parameters
function New-DevEnvironment {
    param(
        [Parameter(Mandatory=$true)]
        [string]$ProjectName,
        
        [Parameter(Mandatory=$false)]
        [string]$Location = "C:\Projects"
    )
    
    $projectPath = Join-Path $Location $ProjectName
    
    try {
        New-Item -Path $projectPath -ItemType Directory -Force
        Write-Output "Created project directory at $projectPath"
    }
    catch {
        Write-Error "Failed to create project directory: $_"
    }
}
```

### 2. Windows Automation
```powershell
# Service Management
function Manage-WindowsServices {
    param(
        [string[]]$ServiceNames,
        [string]$Action
    )
    
    foreach ($service in $ServiceNames) {
        try {
            switch ($Action) {
                'Start' { Start-Service -Name $service }
                'Stop'  { Stop-Service -Name $service }
                'Restart' { Restart-Service -Name $service }
            }
        }
        catch {
            Write-Error "Failed to $Action service $service: $_"
        }
    }
}
```

## Ruby

### 1. Basic Syntax
```ruby
#!/usr/bin/env ruby

require 'logger'

# Configure logger
logger = Logger.new(STDOUT)
logger.level = Logger::INFO

# Class for deployment operations
class Deployer
  def initialize(environment)
    @environment = environment
    @logger = Logger.new(STDOUT)
  end

  def deploy
    @logger.info("Starting deployment to #{@environment}")
    # Deployment logic
    @logger.info("Deployment completed")
  rescue StandardError => e
    @logger.error("Deployment failed: #{e.message}")
    raise
  end
end
```

### 2. Automation Tasks
```ruby
# Configuration Management
require 'yaml'

class ConfigManager
  def self.load_config(file)
    YAML.load_file(file)
  rescue StandardError => e
    puts "Error loading configuration: #{e.message}"
    {}
  end

  def self.save_config(file, config)
    File.write(file, config.to_yaml)
  rescue StandardError => e
    puts "Error saving configuration: #{e.message}"
    false
  end
end
```

## Language Selection Criteria

### 1. Use Case Considerations
- System requirements
- Platform compatibility
- Team expertise
- Performance needs
- Integration requirements
- Maintenance overhead

### 2. Language Strengths
- Bash: System operations
- Python: Versatility and libraries
- PowerShell: Windows automation
- Ruby: Configuration management

## Best Practices

### 1. Code Organization
```python
# Example of well-organized Python script
#!/usr/bin/env python3

import argparse
import logging
import sys
from typing import List

# Configure logging
def setup_logging(level: str = "INFO") -> None:
    logging.basicConfig(
        level=level,
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )

# Parse arguments
def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser()
    parser.add_argument('--input', required=True)
    return parser.parse_args()

# Main logic
def main() -> int:
    args = parse_args()
    setup_logging()
    try:
        # Main application logic
        return 0
    except Exception as e:
        logging.error(f"Error: {e}")
        return 1

if __name__ == "__main__":
    sys.exit(main())
```

### 2. Error Handling
```bash
# Example of proper error handling in Bash
#!/bin/bash

set -e  # Exit on error
set -u  # Exit on undefined variable

# Error handler
trap 'echo "Error on line $LINENO"' ERR

# Function with error handling
deploy_application() {
    if ! command -v docker &> /dev/null; then
        echo "Error: Docker is not installed" >&2
        return 1
    }
    
    if ! docker build -t myapp .; then
        echo "Error: Docker build failed" >&2
        return 1
    fi
}
```

## Testing and Validation

### 1. Unit Testing
```python
# Python unit test example
import unittest

class TestDeployment(unittest.TestCase):
    def setUp(self):
        self.deployer = Deployer('test')
    
    def test_deployment_success(self):
        result = self.deployer.deploy()
        self.assertTrue(result)
    
    def test_deployment_failure(self):
        with self.assertRaises(DeploymentError):
            self.deployer.deploy(invalid_param=True)

if __name__ == '__main__':
    unittest.main()
```

### 2. Integration Testing
```ruby
# Ruby integration test example
require 'minitest/autorun'
require './deployment_system'

class TestDeploymentIntegration < Minitest::Test
  def setup
    @system = DeploymentSystem.new
    @system.connect_to_test_environment
  end

  def test_full_deployment_cycle
    assert @system.prepare_deployment
    assert @system.deploy
    assert @system.verify_deployment
  end
end
```

## Conclusion

Choosing the right scripting language and following best practices are crucial for effective DevOps automation. Each language has its strengths and ideal use cases.

## Next Steps
1. Evaluate project requirements
2. Select appropriate language
3. Set up development environment
4. Implement best practices
5. Establish testing procedures
6. Monitor and maintain scripts
