# Kubernetes

## Introduction

Kubernetes (K8s) is an open-source container orchestration platform for automating deployment, scaling, and management of containerized applications. This guide covers Kubernetes architecture, components, and best practices.

## Core Concepts

### 1. Architecture Components
- Control Plane
- Nodes
- Pods
- Services
- Controllers
- Volumes

### 2. Basic Objects
```yaml
# Example Pod
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

## Cluster Setup

### 1. Local Development
```bash
# Minikube installation
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start cluster
minikube start

# Verify installation
kubectl cluster-info
```

### 2. Production Setup
```bash
# kubeadm installation
sudo apt-get update
sudo apt-get install -y kubeadm kubelet kubectl

# Initialize cluster
sudo kubeadm init

# Set up networking
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## Workload Resources

### 1. Deployments
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

### 2. StatefulSets
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

## Networking

### 1. Services
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```

### 2. Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

## Storage

### 1. Persistent Volumes
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0001
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  storageClassName: slow
  hostPath:
    path: /data
```

### 2. Storage Claims
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: slow
```

## Configuration

### 1. ConfigMaps
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app.properties: |
    environment=production
    debug=false
  logging.properties: |
    log_level=INFO
```

### 2. Secrets
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: dXNlcm5hbWU=
  password: cGFzc3dvcmQ=
```

## Security

### 1. RBAC
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

### 2. Network Policies
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-allow
spec:
  podSelector:
    matchLabels:
      app: api
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
```

## Monitoring and Logging

### 1. Resource Metrics
```yaml
apiVersion: metrics.k8s.io/v1beta1
kind: MetricsList
metadata:
  name: pods
items:
- timestamp: "2023-01-01T00:00:00Z"
  window: "1m0s"
  pods:
    - name: nginx-pod
      usage:
        cpu: "100m"
        memory: "256Mi"
```

### 2. Logging Configuration
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args: [/bin/sh, -c, 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
```

## Resource Management

### 1. Resource Quotas
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 4Gi
    limits.cpu: "8"
    limits.memory: 8Gi
```

### 2. Limit Ranges
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
      cpu: 1
    defaultRequest:
      memory: 256Mi
      cpu: 0.5
    type: Container
```

## High Availability

### 1. Pod Disruption Budgets
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: nginx
```

### 2. Affinity Rules
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/e2e-az-name
            operator: In
            values:
            - e2e-az1
            - e2e-az2
```

## Best Practices

### 1. Application Design
- Use deployments
- Implement health checks
- Resource management
- Proper labeling
- Version control

### 2. Security
- RBAC implementation
- Network policies
- Secret management
- Container security
- Regular updates

## Troubleshooting

### 1. Common Issues
```bash
# Check pod status
kubectl describe pod pod-name

# View logs
kubectl logs pod-name

# Check events
kubectl get events

# Debug with temporary pod
kubectl run tmp-shell --rm -i --tty --image nginx -- /bin/bash
```

### 2. Debug Commands
```bash
# Port forwarding
kubectl port-forward pod-name 8080:80

# Exec into container
kubectl exec -it pod-name -- /bin/bash

# Copy files
kubectl cp pod-name:/path/to/file ./local/path
```

## Conclusion

Kubernetes provides a robust platform for container orchestration. Understanding its concepts and best practices is crucial for successful container deployment and management.

## Next Steps
1. Set up local cluster
2. Deploy first application
3. Configure networking
4. Implement security
5. Set up monitoring
6. Plan for scaling
