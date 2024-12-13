# Apache JMeter Guide

## Overview

### Introduction
```yaml
Description:
  Load testing tool for analyzing and measuring performance of applications

Key Features:
  - Performance testing
  - Load testing
  - Stress testing
  - Distributed testing
  - Multiple protocols
  - Extensible architecture
```

### Installation
```bash
# Download JMeter
wget https://downloads.apache.org/jmeter/binaries/apache-jmeter-x.x.tgz

# Extract and run
tar -xvf apache-jmeter-x.x.tgz
cd apache-jmeter-x.x/bin
./jmeter
```

## Test Plan Creation

### Basic Structure
```yaml
Test Plan:
  - Thread Groups
  - Samplers
  - Controllers
  - Listeners
  - Timers
  - Assertions
  - Configuration Elements
```

### Thread Group Configuration
```yaml
Thread Group Settings:
  Number of Threads: 100
  Ramp-up Period: 60
  Loop Count: -1 (infinite)
  Duration: 3600 seconds

Options:
  - Same user on each iteration
  - Delay thread creation
  - Specify thread lifetime
```

## Test Elements

### HTTP Request Sampler
```yaml
HTTP Request:
  Server:
    - Protocol: http/https
    - Domain: example.com
    - Port: 80/443
    - Path: /api/endpoint

  Parameters:
    - Method: GET/POST/PUT/DELETE
    - Parameters
    - Body Data
    - Files
```

### Controllers
```yaml
Logic Controllers:
  - If Controller
  - Loop Controller
  - While Controller
  - Transaction Controller
  - Random Controller

Example:
  If Controller:
    Condition: ${VAR} == "value"
    Evaluate for all children: true
```

## Advanced Features

### Timers
```yaml
Timer Types:
  Constant Timer:
    - Fixed delay between requests
    
  Gaussian Timer:
    - Random delay with normal distribution
    
  Uniform Timer:
    - Random delay with uniform distribution
    
  Synchronizing Timer:
    - Synchronize multiple threads
```

### Assertions
```yaml
Assertion Types:
  Response:
    - Response Code
    - Response Time
    - Size
    - Content
    
  Pattern Matching:
    - Contains
    - Matches
    - Equals
    - Substring
```

## Test Data Management

### CSV Data Set
```yaml
CSV Configuration:
  Filename: test-data.csv
  Variable Names: username,password
  Delimiter: ,
  Recycle: true
  Stop thread on EOF: false
  Sharing mode: All threads

Example CSV:
  user1,pass1
  user2,pass2
  user3,pass3
```

### User Variables
```yaml
User Defined Variables:
  - BASE_URL: http://example.com
  - TIMEOUT: 5000
  - USER_COUNT: 100

User Parameters:
  - Per-thread values
  - Randomization
  - Calculated values
```

## Performance Monitoring

### Listeners
```yaml
Listener Types:
  - View Results Tree
  - Summary Report
  - Aggregate Report
  - Graph Results
  - Response Time Graph
  - Active Threads Over Time

Configuration:
  - Save to file
  - Error logging
  - Sample logging
```

### Reports
```yaml
Report Types:
  Dashboard:
    - Statistics
    - Graphs
    - Error analysis
    
  Custom Reports:
    - JTL files
    - CSV output
    - Custom listeners
```

## Distributed Testing

### Master-Slave Setup
```yaml
Master Configuration:
  - Remote hosts list
  - Test plan distribution
  - Result collection

Slave Configuration:
  - JMeter server mode
  - Port configuration
  - Resource allocation
```

### Remote Testing
```bash
# Start JMeter server on slave
jmeter-server -Djava.rmi.server.hostname=slave-ip

# Run test from master
jmeter -n -t test.jmx -R slave1,slave2 -l results.jtl
```

## Best Practices

### Test Design
```yaml
Guidelines:
  - Clear test structure
  - Proper thread groups
  - Appropriate timers
  - Error handling
  - Result validation

Anti-patterns:
  - Over-complex tests
  - Missing assertions
  - Hard-coded values
  - Resource leaks
```

### Performance Optimization
```yaml
Optimization Areas:
  JMeter Configuration:
    - Memory allocation
    - Thread management
    - Listener usage
    
  Test Execution:
    - GUI mode vs Non-GUI
    - Resource monitoring
    - Result handling
```

## Command Line Operation

### CLI Commands
```bash
# Run test plan
jmeter -n -t test.jmx -l log.jtl

# Generate report
jmeter -g log.jtl -o report-output

# Run with properties
jmeter -n -t test.jmx -J property=value

# Distributed testing
jmeter -n -t test.jmx -R host1,host2 -l results.jtl
```

## Integration

### CI/CD Integration
```yaml
Jenkins Pipeline:
  stages:
    - name: Performance Test
      steps:
        - sh 'jmeter -n -t test.jmx -l results.jtl'
        - sh 'jmeter -g results.jtl -o report'

GitHub Actions:
  - name: Run JMeter Test
    run: |
      ./jmeter -n -t test.jmx -l results.jtl
```

### Monitoring Integration
```yaml
Integration Points:
  - InfluxDB
  - Grafana
  - Elasticsearch
  - Prometheus

Metrics:
  - Response times
  - Error rates
  - Throughput
  - Resource usage
```
