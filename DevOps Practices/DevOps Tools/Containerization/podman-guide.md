# Podman

## Introduction

Podman (Pod Manager) is a daemonless container engine for developing, managing, and running containers on Linux systems. This guide covers Podman concepts, installation, usage, and best practices.

## Core Concepts

### 1. Key Features
- Daemonless architecture
- Rootless containers
- Pod support
- OCI compliance
- Docker compatibility
- SystemD integration

### 2. Architecture
- No daemon requirement
- Direct container execution
- User-space operation
- Namespace separation
- cgroup management

## Installation

### 1. Linux Installation
```bash
# RHEL/CentOS
sudo dnf install podman

# Ubuntu/Debian
sudo apt-get install podman

# Verify installation
podman --version
```

### 2. Initial Setup
```bash
# Configure registries
sudo vi /etc/containers/registries.conf

# Set up storage
sudo vi /etc/containers/storage.conf

# Test installation
podman run hello-world
```

## Basic Commands

### 1. Container Operations
```bash
# Run container
podman run -d -p 8080:80 nginx

# List containers
podman ps
podman ps -a

# Container management
podman start container_name
podman stop container_name
podman rm container_name

# Container inspection
podman inspect container_name
```

### 2. Image Management
```bash
# Pull image
podman pull nginx:latest

# List images
podman images

# Remove image
podman rmi nginx:latest

# Build image
podman build -t myapp:1.0 .
```

## Podman Containers

### 1. Running Containers
```bash
# Basic run
podman run -d -p 8080:80 myapp:1.0

# Run with environment variables
podman run -d \
  -e DATABASE_URL=postgres://db \
  -p 8080:80 \
  myapp:1.0

# Run with volume
podman run -d \
  -v ./data:/app/data:Z \
  -p 8080:80 \
  myapp:1.0
```

### 2. Container Management
```bash
# Execute command in container
podman exec -it container_name /bin/bash

# View logs
podman logs container_name

# Copy files
podman cp file.txt container_name:/path/

# Update container
podman update --cpus 2 container_name
```

## Pod Management

### 1. Pod Operations
```bash
# Create pod
podman pod create --name mypod -p 8080:80

# Run container in pod
podman run -d --pod mypod nginx

# List pods
podman pod list

# Pod management
podman pod start mypod
podman pod stop mypod
podman pod rm mypod
```

### 2. Pod Configuration
```yaml
# pod-config.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: web
      image: nginx
      ports:
        - containerPort: 80
```

## Networking

### 1. Network Operations
```bash
# Create network
podman network create mynetwork

# List networks
podman network ls

# Connect container
podman network connect mynetwork container_name

# Remove network
podman network rm mynetwork
```

### 2. Network Configuration
```bash
# Inspect network
podman network inspect mynetwork

# Port mapping
podman run -p 8080:80 nginx

# DNS configuration
podman run --dns 8.8.8.8 nginx
```

## Volume Management

### 1. Volume Operations
```bash
# Create volume
podman volume create myvolume

# List volumes
podman volume ls

# Mount volume
podman run -v myvolume:/data nginx

# Remove volume
podman volume rm myvolume
```

### 2. Volume Configuration
```bash
# Inspect volume
podman volume inspect myvolume

# Backup volume
podman volume export myvolume > backup.tar

# Restore volume
podman volume import myvolume backup.tar
```

## Security Features

### 1. Rootless Containers
```bash
# Enable user namespaces
sudo sysctl -w kernel.unprivileged_userns_clone=1

# Run rootless container
podman run --user 1000:1000 nginx
```

### 2. SELinux Integration
```bash
# Run with SELinux context
podman run --security-opt label=level:s0:c100,c200 nginx

# Volume labeling
podman run -v ./data:/data:Z nginx
```

## SystemD Integration

### 1. Container Services
```ini
# container-web.service
[Unit]
Description=Web Container
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/podman run --rm nginx
ExecStop=/usr/bin/podman stop -t 2 web

[Install]
WantedBy=multi-user.target
```

### 2. Service Management
```bash
# Generate service file
podman generate systemd container_name > container-web.service

# Install service
sudo mv container-web.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now container-web
```

## Best Practices

### 1. Container Management
- Use rootless containers
- Implement resource limits
- Regular updates
- Proper logging
- Security scanning

### 2. Image Management
- Minimal base images
- Multi-stage builds
- Version tagging
- Regular updates
- Vulnerability scanning

## Troubleshooting

### 1. Common Issues
- Permission problems
- Network connectivity
- Resource constraints
- Storage issues
- SELinux conflicts

### 2. Debug Commands
```bash
# Debug information
podman info

# System events
podman events

# Resource usage
podman stats

# Health check
podman healthcheck run container_name
```

## Migration from Docker

### 1. Command Mapping
```bash
# Docker to Podman mapping
docker run -> podman run
docker-compose -> podman-compose
docker build -> podman build
```

### 2. Configuration
```bash
# Docker compatibility
alias docker=podman

# Docker-compose compatibility
pip3 install podman-compose
```

## Performance Optimization

### 1. Resource Management
- CPU limits
- Memory constraints
- Storage optimization
- Network tuning
- Logging configuration

### 2. Monitoring
```bash
# Resource monitoring
podman stats

# System resources
podman system df

# Performance metrics
podman events
```

## Conclusion

Podman provides a secure, daemonless alternative to traditional container engines. Its rootless capabilities and compatibility with Docker make it an excellent choice for container management.

## Next Steps
1. Install Podman
2. Create first container
3. Set up pods
4. Configure networking
5. Implement security measures
6. Migrate from Docker if needed
