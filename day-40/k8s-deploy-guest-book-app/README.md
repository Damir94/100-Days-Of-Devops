# Guestbook Application — Kubernetes Deployment Guide

## Overview
The Guestbook Application is a simple multi-tier web app designed to demonstrate a Kubernetes deployment workflow.
It allows users to record and view guest entries through a PHP frontend that communicates with a Redis database (master/slave setup) for data persistence.
This document provides a complete step-by-step guide and YAML manifests for deploying the Guestbook Application on a Kubernetes cluster.

## Architecture
The application consists of three tiers:
| Tier          | Component       | Purpose                                                |
| ------------- | --------------- | ------------------------------------------------------ |
| **Back-End**  | Redis Master    | Stores and manages guestbook entries                   |
| **Back-End**  | Redis Slave     | Replicates data from Redis Master for read scalability |
| **Front-End** | PHP Application | Provides web interface for users to view/add entries   |

1. Redis Master Deployment and Service
#### Purpose
The Redis Master handles write operations from the Guestbook frontend. It acts as the primary data source, while slaves replicate from it.

redis-master-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
      role: master
  template:
    metadata:
      labels:
        app: redis
        role: master
    spec:
      containers:
        - name: master-redis-xfusion
          image: redis
          resources:
            requests:
              cpu: "100m"
              memory: "100Mi"
          ports:
            - containerPort: 6379
```
### Explanation
  - replicas: Only 1 master pod to maintain a single source of truth.
  - image: Uses the official redis image.
  - resources.requests: Reserves 100m CPU and 100Mi memory for predictable performance.
  - containerPort: Exposes Redis default port 6379.

redis-master-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-master
spec:
  selector:
    app: redis
    role: master
  ports:
    - port: 6379
      targetPort: 6379
```
### Explanation
  - Service type: Default (ClusterIP), exposing Redis master internally.
  - Selector: Routes traffic to pods labeled with app: redis and role: master.
  - Port mapping: Maps service port 6379 to container port 6379.

2. Redis Slave Deployment and Service
#### Purpose
The Redis Slave handles read operations and replicates data from the master.
This setup increases scalability and availability for the app’s backend.

redis-slave-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis
      role: slave
  template:
    metadata:
      labels:
        app: redis
        role: slave
    spec:
      containers:
        - name: slave-redis-xfusion
          image: gcr.io/google_samples/gb-redisslave:v3
          resources:
            requests:
              cpu: "100m"
              memory: "100Mi"
          env:
            - name: GET_HOSTS_FROM
              value: "dns"
          ports:
            - containerPort: 6379
```
### Explanation
  - replicas: Two Redis slave pods for redundancy and load distribution.
  - image: Uses prebuilt Google sample image for Redis replication.environment variable:
    - GET_HOSTS_FROM=dns tells the pod to resolve Redis master by service name.
  - resources: Matches the master’s resource configuration for consistency.

redis-slave-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
spec:
  selector:
    app: redis
    role: slave
  ports:
    - port: 6379
      targetPort: 6379
```
### Explanation
  - Provides an internal endpoint for the frontend to access Redis slaves.
  - Uses DNS name redis-slave within the cluster for connection.

3. Frontend Deployment and Service
### Purpose
The frontend is a PHP application that communicates with the Redis backend.
It provides a user interface for visitors to view and add guestbook entries.

frontend-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: guestbook
      tier: frontend
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
        - name: php-redis-xfusion
          image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
          resources:
            requests:
              cpu: "100m"
              memory: "100Mi"
          env:
            - name: GET_HOSTS_FROM
              value: "dns"
          ports:
            - containerPort: 80
```
### Explanation
  - replicas: 3 for load balancing and high availability.
  - image: Uses the official Google Guestbook frontend.
  - port 80: Exposes HTTP traffic.
  - GET_HOSTS_FROM=dns: Ensures Redis hosts are resolved by DNS (service names).

frontend-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: NodePort
  selector:
    app: guestbook
    tier: frontend
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30009
```
### Explanation
  - type: NodePort makes the app accessible externally through the node’s IP.
  - port: The internal service port (80).
  - nodePort: Exposes the application externally on port 30009.

### Directory Structure
Your project directory should look like this:
```bash
guestbook/
├── redis-master-deployment.yaml
├── redis-master-service.yaml
├── redis-slave-deployment.yaml
├── redis-slave-service.yaml
├── frontend-deployment.yaml
├── frontend-service.yaml
└── README.md
```

### Deployment Steps

1. Apply Redis Master:
```bash
kubectl apply -f redis-master-deployment.yaml
kubectl apply -f redis-master-service.yaml
```
2. Apply Redis Slave:
```bash
kubectl apply -f redis-slave-deployment.yaml
kubectl apply -f redis-slave-service.yaml
```
3. Apply Frontend:
```bash
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml
```
4. Verify Resources:
```bash
kubectl get all
```
5. Access the App:
Open a browser and visit:
```bash
http://<NodeIP>:30009
```
<img width="1354" height="314" alt="Screenshot 2025-10-26 at 8 35 50 PM" src="https://github.com/user-attachments/assets/d9bf5aaf-f796-470c-84ae-d252d6cc47e2" />


### Verification Checklist
| Check               | Command            | Expected Result                                  |
| ------------------- | ------------------ | ------------------------------------------------ |
| Pods running        | `kubectl get pods` | All pods show **Running**                        |
| Services active     | `kubectl get svc`  | 3 services (redis-master, redis-slave, frontend) |
| Frontend accessible | Browser access     | Guestbook UI loads successfully                  |

### Best Practices & Notes
  - Use Namespaces (e.g., guestbook) in production for isolation.
  - Monitor resource usage to fine-tune requests and add limits.
  - Replace NodePort with Ingress or LoadBalancer in production clusters.
  - Use ConfigMaps or Secrets for configurable environment values.

### Conclusion
You have successfully deployed a multi-tier Guestbook Application on Kubernetes!
This setup demonstrates:
  - Pod deployments for stateful and stateless components
  - Internal service discovery via DNS
  - Resource management using requests
  - External exposure through NodePort
