# Node Application Deployment on Kubernetes

## Overview

The Nautilus Development and DevOps teams collaborated to deploy a Node.js application on a Kubernetes cluster. 
This deployment ensures scalability, reliability, and accessibility through a NodePort service.
This document describes the steps, configurations, and verification process used to successfully deploy and expose the application.

## Objective
The goal of this task was to:
 - Deploy a Node.js application containerized using the image gcr.io/kodekloud/centos-ssh-enabled:node.
 - Ensure 2 replicas of the application are always running.
 - Expose the deployment externally using a NodePort Service with specific port configurations.

## Kubernetes Deployment Details
1. Deployment Configuration
A Kubernetes Deployment named node-app was created with the following specifications:
 - Image: gcr.io/kodekloud/centos-ssh-enabled:node
 - Replicas: 2
 - Container Port: 8080
 - Label Selector: app: node-app

Deployment YAML:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
  labels:
    app: node-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
        - name: node-app
          image: gcr.io/kodekloud/centos-ssh-enabled:node
          ports:
            - containerPort: 8080
```
This deployment ensures that if any pod fails, Kubernetes automatically replaces it to maintain two running replicas.

2. Service Configuration
A NodePort Service named node-app-service was created to expose the application outside the cluster.
 - Service Type: NodePort
 - Port: 8080
 - Target Port: 8080
 - Node Port: 30012
 - Selector: app: node-app

Service YAML:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
spec:
  type: NodePort
  selector:
    app: node-app
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30012
```
This configuration allows users to access the application using the Kubernetes node’s IP and port 30012.

Verification Steps
Once both YAML files were applied, the following commands were used to verify the deployment:

Check running pods
```bash
kubectl get pods
```

Expected output: 2 pods in Running state.

Check service status
```bash
kubectl get svc
```

Expected output:
```yaml
node-app-service   NodePort   ...   8080:30012/TCP
```
Access the application
You can access the running Node.js application via:
```cpp
http://<Node-IP>:30012
```

<img width="1700" height="875" alt="Screenshot 2025-10-18 at 9 32 27 AM" src="https://github.com/user-attachments/assets/723c6f24-b344-4865-8aa4-8cb89954ae98" />

## Summary
 - Deployment: Node.js app using gcr.io/kodekloud/centos-ssh-enabled:node
 - Replicas: 2 running pods
 - Service Type: NodePort (Port 8080 → NodePort 30012)
 - Verification: Successful — pods running and app accessible via NodePort

📂 Repository Structure
.
├── node-app-deployment.yaml
├── node-app-service.yaml
└── README.md

