# 100 Days 0f Devops - Day 49: Creating an HTTPD Deployment in Kubernetes

## Overview
As part of the Nautilus DevOps team’s journey into Kubernetes-based application management, one team member was assigned to deploy an Apache HTTP Server (httpd) using a Kubernetes Deployment.
This guide documents the steps and explains the logic behind the configuration.

## Task Objective
Create a Kubernetes Deployment that:
 - Is named httpd
 - Uses the container image httpd:latest
 - Manages Pods running the Apache HTTP Server

## Deployment Manifest
The deployment is defined as follows:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
  labels:
    app: httpd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
        - name: httpd
          image: httpd:latest
```

## Steps to Deploy
1. Create the manifest file
```bash
vi httpd-deployment.yaml
```

2. Apply the deployment
```bash
kubectl apply -f httpd-deployment.yaml
```

3. Check deployment status
```bash
kubectl get deployments
```

4. View Pods created by the deployment
```bash
kubectl get pods
```

5. Describe deployment (optional)
```bash
kubectl describe deployment httpd
```
 
## Expected Result

After successful deployment:
 - A Deployment named httpd will be created.
 - One Pod will be running an Apache HTTP Server using the httpd:latest image.
 - The Pods can be scaled easily using the Deployment.

## Key Takeaways
 - Deployments provide replica management, auto-healing, and rolling updates for applications.
 - The httpd Deployment acts as a controlled, scalable way to run a web server.
 - Labels (app: httpd) help group and manage related resources efficiently.

