# Helm

## Introduction

Helm is the package manager for Kubernetes, helping you manage Kubernetes applications through Helm Charts. This guide covers Helm concepts, usage, and best practices for package management in Kubernetes.

## Core Concepts

### 1. Basic Components
- Charts
- Repositories
- Releases
- Values
- Templates
- Hooks

### 2. Chart Structure
```plaintext
mychart/
  Chart.yaml          # Chart metadata
  values.yaml         # Default configuration
  charts/            # Chart dependencies
  templates/         # Template files
  README.md          # Documentation
  LICENSE           # License information
```

## Installation and Setup

### 1. Installation
```bash
# Using package manager
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Verify installation
helm version

# Add official repository
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

### 2. Initial Configuration
```bash
# Initialize a new chart
helm create mychart

# Lint a chart
helm lint mychart

# Package a chart
helm package mychart
```

## Chart Development

### 1. Chart.yaml
```yaml
apiVersion: v2
name: mychart
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.0.0"
dependencies:
  - name: mysql
    version: 8.8.8
    repository: https://charts.helm.sh/stable
```

### 2. values.yaml
```yaml
# Default values
replicaCount: 1
image:
  repository: nginx
  tag: stable
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80
```

## Template Development

### 1. Basic Template
```yaml
# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    {{- include "mychart.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "mychart.selectorLabels" . | nindent 6 }}
```

### 2. Helper Functions
```yaml
# templates/_helpers.tpl
{{/* Generate basic labels */}}
{{- define "mychart.labels" -}}
helm.sh/chart: {{ include "mychart.chart" . }}
{{ include "mychart.selectorLabels" . }}
{{- end }}
```

## Chart Management

### 1. Installation Commands
```bash
# Install chart
helm install release-name ./mychart

# Install with custom values
helm install release-name ./mychart -f custom-values.yaml

# Install with set values
helm install release-name ./mychart --set service.type=LoadBalancer
```

### 2. Update and Rollback
```bash
# Upgrade release
helm upgrade release-name ./mychart

# Rollback to previous version
helm rollback release-name 1

# List release history
helm history release-name
```

## Repository Management

### 1. Repository Operations
```bash
# Add repository
helm repo add myrepo https://charts.example.com

# Update repositories
helm repo update

# List repositories
helm repo list

# Remove repository
helm repo remove myrepo
```

### 2. Chart Search
```bash
# Search charts
helm search repo mysql

# Show chart information
helm show chart stable/mysql

# Show all chart information
helm show all stable/mysql
```

## Dependencies

### 1. Chart Dependencies
```yaml
# Chart.yaml
dependencies:
  - name: postgresql
    version: 10.3.11
    repository: https://charts.bitnami.com/bitnami
    condition: postgresql.enabled
```

### 2. Dependency Management
```bash
# Update dependencies
helm dependency update

# Build dependencies
helm dependency build

# List dependencies
helm dependency list
```

## Testing

### 1. Test Templates
```yaml
# templates/tests/test-connection.yaml
apiVersion: v1
kind: Pod
metadata:
  name: "{{ include "mychart.fullname" . }}-test-connection"
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['{{ include "mychart.fullname" . }}:{{ .Values.service.port }}']
  restartPolicy: Never
```

### 2. Running Tests
```bash
# Run tests
helm test release-name

# Debug template rendering
helm template ./mychart --debug
```

## Best Practices

### 1. Chart Development
- Use semantic versioning
- Document all values
- Include README
- Follow naming conventions
- Implement tests
- Use helpers

### 2. Security
- Set resource limits
- Define RBAC
- Secure secrets
- Use latest versions
- Validate input

## Common Patterns

### 1. Conditional Resources
```yaml
{{- if .Values.configmap.enabled }}
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "mychart.fullname" . }}
data:
  {{- toYaml .Values.configmap.data | nindent 2 }}
{{- end }}
```

### 2. Named Templates
```yaml
{{/* Define a template */}}
{{- define "mychart.labels" -}}
app: {{ .Chart.Name }}
release: {{ .Release.Name }}
{{- end }}

{{/* Use the template */}}
metadata:
  labels:
    {{- template "mychart.labels" . }}
```

## Troubleshooting

### 1. Common Issues
- Template rendering errors
- Dependency issues
- Version conflicts
- Release failures
- Resource conflicts

### 2. Debug Commands
```bash
# Debug installation
helm install --debug --dry-run release-name ./mychart

# Get release status
helm status release-name

# Get release manifest
helm get manifest release-name
```

## Conclusion

Helm simplifies Kubernetes application management through package management and templating. Understanding its concepts and best practices ensures efficient Kubernetes deployments.

## Next Steps
1. Install Helm
2. Create first chart
3. Explore official charts
4. Develop templates
5. Implement testing
6. Deploy applications
