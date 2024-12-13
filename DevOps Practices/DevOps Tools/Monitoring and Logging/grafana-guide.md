# Grafana

## Introduction

Grafana is an open-source platform for monitoring and observability, providing tools to query, visualize, alert on, and explore metrics, logs, and traces. This guide covers Grafana setup, configuration, and best practices.

## Core Concepts

### 1. Basic Components
- Dashboards
- Panels
- Data Sources
- Organizations
- Users
- Alerts

### 2. Architecture
- Web Interface
- Backend Server
- Database
- Plugin System
- Alert Engine

## Installation

### 1. Package Installation
```bash
# Ubuntu/Debian
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install grafana

# Start service
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

### 2. Docker Installation
```yaml
version: '3'
services:
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    volumes:
      - grafana-storage:/var/lib/grafana
volumes:
  grafana-storage:
```

## Configuration

### 1. Basic Settings
```ini
# grafana.ini
[server]
protocol = http
http_port = 3000
domain = localhost

[security]
admin_user = admin
admin_password = admin

[auth]
disable_login_form = false
```

### 2. Data Sources
```yaml
# datasources.yaml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

## Dashboard Creation

### 1. JSON Model
```json
{
  "dashboard": {
    "id": null,
    "title": "Production Overview",
    "tags": ["production", "metrics"],
    "timezone": "browser",
    "panels": [
      {
        "title": "CPU Usage",
        "type": "graph",
        "datasource": "Prometheus",
        "targets": [
          {
            "expr": "100 - (avg by(instance)(irate(node_cpu_seconds_total{mode='idle'}[5m])) * 100)"
          }
        ]
      }
    ]
  }
}
```

### 2. Dashboard Provisioning
```yaml
# dashboards.yaml
apiVersion: 1
providers:
  - name: 'default'
    orgId: 1
    folder: ''
    type: file
    options:
      path: /var/lib/grafana/dashboards
```

## Panel Types

### 1. Graph Panel
```json
{
  "type": "graph",
  "title": "Request Rate",
  "gridPos": {
    "h": 8,
    "w": 12,
    "x": 0,
    "y": 0
  },
  "targets": [
    {
      "expr": "rate(http_requests_total[5m])",
      "legendFormat": "{{method}} {{handler}}"
    }
  ]
}
```

### 2. Stats Panel
```json
{
  "type": "stat",
  "title": "Active Users",
  "targets": [
    {
      "expr": "sum(active_users)",
      "instant": true
    }
  ],
  "options": {
    "colorMode": "value",
    "graphMode": "area"
  }
}
```

## Alerting

### 1. Alert Rules
```json
{
  "alert": {
    "name": "High CPU Usage",
    "conditions": [
      {
        "evaluator": {
          "params": [90],
          "type": "gt"
        },
        "query": {
          "params": ["A", "5m", "now"]
        },
        "reducer": {
          "type": "avg"
        },
        "type": "query"
      }
    ]
  }
}
```

### 2. Notification Channels
```yaml
notifiers:
  - name: Email
    type: email
    settings:
      addresses: alerts@example.com
  - name: Slack
    type: slack
    settings:
      url: https://hooks.slack.com/services/xxx/yyy/zzz
```

## Authentication

### 1. LDAP Configuration
```ini
# ldap.toml
[[servers]]
host = "ldap.example.com"
port = 389
use_ssl = false
bind_dn = "cn=admin,dc=example,dc=com"
bind_password = "admin"
search_filter = "(uid=%s)"
search_base_dns = ["dc=example,dc=com"]
```

### 2. OAuth Configuration
```ini
[auth.google]
enabled = true
client_id = "your_client_id"
client_secret = "your_client_secret"
scopes = ["openid", "email", "profile"]
auth_url = "https://accounts.google.com/o/oauth2/auth"
token_url = "https://accounts.google.com/o/oauth2/token"
```

## API Integration

### 1. API Authentication
```bash
# Create API key
curl -X POST -H "Content-Type: application/json" \
  -d '{"name":"apikeycurl", "role": "Admin"}' \
  http://admin:admin@localhost:3000/api/auth/keys
```

### 2. Dashboard API
```bash
# Get dashboard
curl -H "Authorization: Bearer your_api_key" \
  http://localhost:3000/api/dashboards/uid/your_dashboard_uid
```

## Plugins

### 1. Installation
```bash
# Install plugin
grafana-cli plugins install grafana-clock-panel

# Restart Grafana
sudo systemctl restart grafana-server
```

### 2. Configuration
```ini
[plugins]
enable_alpha = false
allow_loading_unsigned_plugins = false
```

## Performance Tuning

### 1. Database
```ini
[database]
max_open_conn = 300
max_idle_conn = 100
conn_max_lifetime = 14400
```

### 2. Caching
```ini
[caching]
enabled = true
ttl = 600
memory_store = memory
```

## High Availability

### 1. Load Balancer Configuration
```nginx
upstream grafana {
    server grafana1:3000;
    server grafana2:3000;
    server grafana3:3000;
}

server {
    listen 80;
    location / {
        proxy_pass http://grafana;
    }
}
```

### 2. Session Management
```ini
[session]
provider = postgres
provider_config = user=grafana password=password host=localhost port=5432 dbname=grafana
```

## Troubleshooting

### 1. Common Issues
- Data source connection
- Dashboard loading
- Plugin compatibility
- Authentication problems
- Performance issues

### 2. Logging
```ini
[log]
mode = console file
level = info
filters = logging.filter:set_level warn
```

## Best Practices

### 1. Dashboard Design
- Consistent naming
- Logical organization
- Appropriate time ranges
- Clear visualizations
- Helpful documentation

### 2. Security
- Strong passwords
- Role-based access
- Regular updates
- Audit logging
- Secure connections

## Conclusion

Grafana provides powerful visualization and monitoring capabilities. Understanding its features and best practices ensures effective data visualization and monitoring.

## Next Steps
1. Install Grafana
2. Configure data sources
3. Create dashboards
4. Set up alerting
5. Implement authentication
6. Monitor performance
