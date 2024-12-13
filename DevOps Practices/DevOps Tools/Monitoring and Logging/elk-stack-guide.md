# ELK Stack (Elasticsearch, Logstash, Kibana)

## Introduction

The ELK Stack is a collection of three open-source tools: Elasticsearch, Logstash, and Kibana. Together, they provide a powerful solution for searching, analyzing, and visualizing data in real-time.

## Core Components

### 1. Elasticsearch
- Distributed search engine
- Document-oriented database
- RESTful API
- Near real-time search
- Horizontal scalability

### 2. Logstash
- Data collection engine
- Log processing pipeline
- Data transformation
- Input/output plugins
- Filters and codecs

### 3. Kibana
- Data visualization platform
- Analytics dashboard
- Machine learning features
- Monitoring interface
- Security features

## Installation

### 1. Docker Compose Setup
```yaml
version: '3'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.15.0
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.type=single-node
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - 9200:9200
    volumes:
      - elasticsearch-data:/usr/share/elasticsearch/data

  logstash:
    image: docker.elastic.co/logstash/logstash:7.15.0
    ports:
      - 5000:5000
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:7.15.0
    ports:
      - 5601:5601
    environment:
      ELASTICSEARCH_URL: http://elasticsearch:9200
    depends_on:
      - elasticsearch

volumes:
  elasticsearch-data:
```

### 2. Manual Installation
```bash
# Elasticsearch
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.0-linux-x86_64.tar.gz
tar xzf elasticsearch-7.15.0-linux-x86_64.tar.gz
cd elasticsearch-7.15.0/bin
./elasticsearch

# Logstash
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.15.0-linux-x86_64.tar.gz
tar xzf logstash-7.15.0-linux-x86_64.tar.gz
cd logstash-7.15.0
bin/logstash -f config/logstash.conf

# Kibana
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.15.0-linux-x86_64.tar.gz
tar xzf kibana-7.15.0-linux-x86_64.tar.gz
cd kibana-7.15.0/bin
./kibana
```

## Elasticsearch Configuration

### 1. Basic Configuration
```yaml
# elasticsearch.yml
cluster.name: my-cluster
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
discovery.type: single-node

# JVM configuration
-Xms2g
-Xmx2g
```

### 2. Index Settings
```json
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 2,
    "analysis": {
      "analyzer": {
        "custom_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "stop"]
        }
      }
    }
  }
}
```

## Logstash Configuration

### 1. Pipeline Configuration
```ruby
# logstash.conf
input {
  beats {
    port => 5044
  }
  tcp {
    port => 5000
  }
}

filter {
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
  date {
    match => [ "timestamp", "dd/MMM/yyyy:HH:mm:ss Z" ]
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
  stdout {
    codec => rubydebug
  }
}
```

### 2. Plugin Configuration
```ruby
input {
  file {
    path => "/var/log/nginx/access.log"
    start_position => "beginning"
    type => "nginx-access"
  }
}

filter {
  if [type] == "nginx-access" {
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
  }
}
```

## Kibana Configuration

### 1. Basic Configuration
```yaml
# kibana.yml
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://elasticsearch:9200"]
```

### 2. Index Pattern Configuration
```json
{
  "title": "logs-*",
  "timeFieldName": "@timestamp"
}
```

## Security Configuration

### 1. Elasticsearch Security
```yaml
# elasticsearch.yml
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```

### 2. Kibana Security
```yaml
# kibana.yml
elasticsearch.username: "kibana_system"
elasticsearch.password: "password"
xpack.security.enabled: true
```

## Monitoring and Maintenance

### 1. Cluster Health
```bash
# Check cluster health
curl -X GET "localhost:9200/_cluster/health?pretty"

# Node statistics
curl -X GET "localhost:9200/_nodes/stats?pretty"
```

### 2. Index Management
```bash
# Create index
curl -X PUT "localhost:9200/my-index"

# Delete old indices
curl -X DELETE "localhost:9200/logs-2023.01.*"
```

## Data Management

### 1. Index Lifecycle Management
```json
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "rollover": {
            "max_size": "50GB",
            "max_age": "30d"
          }
        }
      },
      "delete": {
        "min_age": "90d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

### 2. Backup and Restore
```bash
# Create snapshot repository
PUT /_snapshot/my_backup
{
  "type": "fs",
  "settings": {
    "location": "/mount/backups/my_backup"
  }
}

# Create snapshot
PUT /_snapshot/my_backup/snapshot_1
```

## Performance Tuning

### 1. Elasticsearch Tuning
```yaml
# elasticsearch.yml
indices.memory.index_buffer_size: 30%
indices.queries.cache.size: 15%
thread_pool.write.queue_size: 1000
```

### 2. Logstash Tuning
```ruby
# logstash.yml
pipeline.workers: 2
pipeline.batch.size: 125
pipeline.batch.delay: 50
```

## Troubleshooting

### 1. Common Issues
- Cluster health
- Memory problems
- Network connectivity
- Index issues
- Pipeline failures

### 2. Debug Commands
```bash
# Elasticsearch
curl -X GET "localhost:9200/_cat/health?v"
curl -X GET "localhost:9200/_cat/indices?v"

# Logstash
bin/logstash --config.test_and_exit -f config/logstash.conf
```

## Best Practices

### 1. Production Deployment
- Use dedicated nodes
- Implement security
- Regular backups
- Monitoring setup
- Resource planning

### 2. Data Management
- Index lifecycle policies
- Data retention
- Shard management
- Backup strategy
- Monitoring alerts

## Conclusion

The ELK Stack provides a comprehensive solution for log management and analysis. Understanding its components and best practices ensures effective implementation and operation.

## Next Steps
1. Install ELK Stack
2. Configure security
3. Set up logging pipeline
4. Create visualizations
5. Implement monitoring
6. Plan maintenance
