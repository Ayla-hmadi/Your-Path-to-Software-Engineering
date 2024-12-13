# Prometheus

## Introduction

Prometheus is an open-source systems monitoring and alerting toolkit. It collects and stores time-series data as metrics, providing a powerful query language and alerting capabilities.

## Core Concepts

### 1. Basic Components
- Prometheus Server
- Targets
- Metrics
- Exporters
- Alert Manager
- Push Gateway

### 2. Data Model
- Time series data
- Labels
- Metrics types
- Sample format
- Timestamps

## Installation

### 1. Binary Installation
```bash
# Download Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.37.0/prometheus-2.37.0.linux-amd64.tar.gz

# Extract and setup
tar xvfz prometheus-*.tar.gz
cd prometheus-*

# Run Prometheus
./prometheus --config.file=prometheus.yml
```

### 2. Docker Installation
```yaml
# docker-compose.yml
version: '3'
services:
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
```

## Configuration

### 1. Basic Configuration
```yaml
# prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

### 2. Advanced Configuration
```yaml
# Advanced scraping configuration
scrape_configs:
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
```

## Metrics Collection

### 1. Exporters
```yaml
# Node exporter configuration
scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
    metrics_path: /metrics
    scheme: http
```

### 2. Custom Metrics
```python
# Python client example
from prometheus_client import start_http_server, Counter
import time

REQUEST_COUNT = Counter('request_count', 'Number of requests')

def process_request():
    REQUEST_COUNT.inc()

if __name__ == '__main__':
    start_http_server(8000)
    while True:
        process_request()
        time.sleep(1)
```

## PromQL (Prometheus Query Language)

### 1. Basic Queries
```promql
# Rate of requests
rate(http_requests_total[5m])

# Current memory usage
node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes

# CPU usage
100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

### 2. Advanced Queries
```promql
# Aggregation
sum(rate(http_requests_total[5m])) by (status_code)

# Range vectors
http_requests_total{job="api"}[5m]

# Instant vectors
http_requests_total{job="api"}
```

## Alerting

### 1. Alert Rules
```yaml
# alerting_rules.yml
groups:
  - name: example
    rules:
    - alert: HighRequestLatency
      expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
      for: 10m
      labels:
        severity: page
      annotations:
        summary: High request latency on {{ $labels.instance }}
```

### 2. Alertmanager Configuration
```yaml
# alertmanager.yml
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: 'web.hook'

receivers:
- name: 'web.hook'
  webhook_configs:
  - url: 'http://127.0.0.1:5001/'
```

## Storage

### 1. Local Storage
```yaml
# Storage configuration
storage:
  tsdb:
    path: /data
    retention.time: 15d
    retention.size: 50GB
```

### 2. Remote Storage
```yaml
# Remote write configuration
remote_write:
  - url: "http://remote-storage:9201/write"
    basic_auth:
      username: "user"
      password: "password"
```

## Integration

### 1. Kubernetes Integration
```yaml
# kubernetes service discovery
scrape_configs:
  - job_name: 'kubernetes-apiservers'
    kubernetes_sd_configs:
    - role: endpoints
    scheme: https
    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
```

### 2. Service Discovery
```yaml
# File-based service discovery
scrape_configs:
  - job_name: 'file_sd'
    file_sd_configs:
      - files:
        - 'targets/*.json'
        refresh_interval: 5m
```

## Best Practices

### 1. Metric Naming
- Use meaningful names
- Follow naming conventions
- Include units
- Use labels effectively
- Keep cardinality reasonable

### 2. Configuration
- Regular backups
- Version control
- Documentation
- Monitoring coverage
- Alert thresholds

## Performance Tuning

### 1. Storage Optimization
```yaml
# Storage tuning
storage:
  tsdb:
    min_block_duration: 2h
    max_block_duration: 24h
    retention.time: 15d
```

### 2. Query Optimization
- Use appropriate time ranges
- Limit label cardinality
- Efficient aggregations
- Cache utilization
- Resource allocation

## Troubleshooting

### 1. Common Issues
- Scrape failures
- Storage problems
- High cardinality
- Query performance
- Alert issues

### 2. Debug Commands
```bash
# Check targets
curl localhost:9090/api/v1/targets

# Query metrics
curl 'localhost:9090/api/v1/query?query=up'

# Check configuration
promtool check config prometheus.yml
```

## Conclusion

Prometheus provides powerful monitoring and alerting capabilities. Understanding its features and best practices ensures effective monitoring of your infrastructure.

## Next Steps
1. Install Prometheus
2. Configure basic monitoring
3. Set up alerting
4. Implement exporters
5. Define recording rules
6. Optimize performance
