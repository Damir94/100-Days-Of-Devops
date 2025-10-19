# 100 Day 0f DevOps - Day 56: Highly Available & Scalable Nginx Deployment on Kubernetes

## Overview
The Nautilus development team is building a static website and wants it to be highly available and scalable on Kubernetes.
To achieve this, we deployed Nginx with multiple replicas and exposed it externally via a NodePort service.

### Design Concept

High Availability
 - The deployment has 3 replicas, ensuring that even if one pod fails, traffic is served by the remaining pods.

Scalability
 - Using a Kubernetes deployment, we can easily scale the number of Nginx pods up or down depending on traffic.

External Access
 - A NodePort service exposes the Nginx deployment on a specific port (30011) on all cluster nodes.
 - This allows developers and testers to access the website using the node IP and the NodePort.

## Implementation Steps

1. Create Deployment
 - Name: nginx-deployment
 - Container: nginx-container using nginx:latest
 - Replicas: 3
 - Container port: 80

2. Create NodePort Service
 - Name: nginx-service
 - Type: NodePort
 - NodePort: 30011
 - Routes traffic to pods labeled app: nginx

## Deployment YAML
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
        - name: nginx-container
          image: nginx:latest
          ports:
            - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30011
```
## Deployment Steps
Apply the YAML:
```bash
kubectl apply -f nginx-deployment.yaml
```
Check the deployment:
```bash
kubectl get deployments
kubectl get pods
kubectl get svc nginx-service
```

Access the website:
```bash
http://<NodeIP>:30011
```

## Key Takeaways
 - Using replicas ensures high availability.
 - Deployments make it easy to scale applications horizontally.
 - NodePort services provide a simple way to expose apps externally.
 - Kubernetes makes it easy to manage static websites in a scalable, fault-tolerant way.

