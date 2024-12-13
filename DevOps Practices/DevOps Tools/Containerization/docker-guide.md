# Docker

## Introduction

Docker is a platform for developing, shipping, and running applications in containers. This guide covers Docker concepts, installation, usage, and best practices for containerization.

## Core Concepts

### 1. Basic Components
- Containers
- Images
- Dockerfile
- Registry
- Docker Engine
- Docker Compose

### 2. Architecture
- Client-server architecture
- Docker daemon
- Docker client
- Docker registry
- Docker objects

## Installation

### 1. Linux Installation
```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# CentOS
sudo yum install docker-ce docker-ce-cli containerd.io

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker
```

### 2. Post-Installation
```bash
# Add user to docker group
sudo usermod -aG docker $USER

# Verify installation
docker --version
docker run hello-world
```

## Dockerfile

### 1. Basic Structure
```dockerfile
# Example Dockerfile
FROM ubuntu:20.04
MAINTAINER author@example.com

# Set environment variables
ENV APP_HOME /app

# Install dependencies
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR $APP_HOME

# Copy application files
COPY . .

# Expose port
EXPOSE 8080

# Define entry point
CMD ["./start.sh"]
```

### 2. Multi-stage Builds
```dockerfile
# Build stage
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Image Management

### 1. Building Images
```bash
# Build image
docker build -t myapp:1.0 .

# Build with different Dockerfile
docker build -f Dockerfile.prod -t myapp:prod .

# Build with build args
docker build --build-arg VERSION=1.0 -t myapp:1.0 .
```

### 2. Registry Operations
```bash
# Tag image
docker tag myapp:1.0 registry.example.com/myapp:1.0

# Push image
docker push registry.example.com/myapp:1.0

# Pull image
docker pull registry.example.com/myapp:1.0
```

## Container Management

### 1. Running Containers
```bash
# Basic run
docker run -d -p 8080:80 myapp:1.0

# Run with environment variables
docker run -d \
  -e DATABASE_URL=postgres://db \
  -p 8080:80 \
  myapp:1.0

# Run with volume
docker run -d \
  -v $(pwd)/data:/app/data \
  -p 8080:80 \
  myapp:1.0
```

### 2. Container Operations
```bash
# List containers
docker ps
docker ps -a

# Stop container
docker stop container_id

# Remove container
docker rm container_id

# View logs
docker logs container_id

# Execute command in container
docker exec -it container_id /bin/bash
```

## Docker Compose

### 1. Basic Configuration
```yaml
# docker-compose.yml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "8080:80"
    environment:
      - NODE_ENV=production
    depends_on:
      - db
  
  db:
    image: postgres:13
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=secret

volumes:
  db-data:
```

### 2. Common Commands
```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs

# Scale service
docker-compose up -d --scale web=3
```

## Networking

### 1. Network Types
- Bridge
- Host
- None
- Overlay
- Macvlan

### 2. Network Operations
```bash
# Create network
docker network create mynetwork

# Run container with network
docker run -d --network mynetwork myapp:1.0

# Inspect network
docker network inspect mynetwork
```

## Volume Management

### 1. Volume Types
- Named volumes
- Bind mounts
- tmpfs mounts

### 2. Volume Operations
```bash
# Create volume
docker volume create myvolume

# Mount volume
docker run -d \
  -v myvolume:/app/data \
  myapp:1.0

# List volumes
docker volume ls

# Remove volume
docker volume rm myvolume
```

## Security Best Practices

### 1. Image Security
- Use official base images
- Scan for vulnerabilities
- Minimize image layers
- Remove unnecessary tools
- Keep images updated

### 2. Container Security
- Run as non-root
- Limit capabilities
- Use security policies
- Regular updates
- Resource constraints

## Performance Optimization

### 1. Image Optimization
- Multi-stage builds
- Layer caching
- Minimal base images
- Proper ordering
- Clean up unused files

### 2. Container Optimization
- Resource limits
- Logging configuration
- Network optimization
- Volume management
- Proper scaling

## Troubleshooting Guide

### 1. Common Issues
- Build failures
- Container crashes
- Network problems
- Volume permissions
- Resource constraints

### 2. Debugging Commands
```bash
# View container details
docker inspect container_id

# View container stats
docker stats

# View container processes
docker top container_id

# View image history
docker history image_id
```

## Development Workflow

### 1. Local Development
```bash
# Development compose file
version: '3.8'
services:
  web:
    build: 
      context: .
      target: development
    volumes:
      - .:/app
    command: npm run dev
```

### 2. Production Deployment
```bash
# Production compose file
version: '3.8'
services:
  web:
    build: 
      context: .
      target: production
    restart: always
    environment:
      - NODE_ENV=production
```

## Conclusion

Docker provides a powerful platform for containerization. Understanding its concepts and best practices ensures efficient container management and deployment.

## Next Steps
1. Set up Docker environment
2. Create first container
3. Implement compose files
4. Configure networking
5. Implement security measures
6. Optimize performance
