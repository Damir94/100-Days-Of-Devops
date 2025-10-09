# Nautilus DevOps — Kubernetes Deployment Setup

## Overview

The Nautilus DevOps team is exploring Kubernetes for containerized application management.
As part of this learning and setup exercise, we created a Deployment resource to manage an httpd (Apache HTTP Server) application within a Kubernetes cluster.
This README provides an overview of the steps, YAML configuration, and concepts behind the task.

## Task Objective
Create a Kubernetes Deployment named httpd that:
 - Deploys the httpd application.
 - Uses the Docker image httpd:latest.
 - Ensures the deployment can automatically manage replicas and handle restarts.

## Deployment Manifest
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
          ports:
            - containerPort: 80
```

## Explanation of Key Fields

| **Field**                        | **Description**                                                        |
| -------------------------------- | ---------------------------------------------------------------------- |
| `apiVersion: apps/v1`            | Specifies the API version for the Deployment resource.                 |
| `kind: Deployment`               | Defines that the resource type being created is a Deployment.          |
| `metadata.name`                  | Names the Deployment as `httpd`.                                       |
| `spec.replicas: 1`               | Ensures one replica (pod) of the application runs.                     |
| `selector`                       | Matches the label `app: httpd` to associate pods with this Deployment. |
| `template`                       | Describes the pod specification that this Deployment manages.          |
| `containers.image: httpd:latest` | Uses the latest `httpd` Docker image from Docker Hub.                  |
| `ports.containerPort: 80`        | Exposes port 80 inside the container for HTTP traffic.                 |


## Steps to Deploy

### 1. Save the manifest file
```bash
vi httpd-deployment.yaml
```

### 2. Apply the deployment
```bash
kubectl apply -f httpd-deployment.yaml
```
### 3. Verify deployment status
```bash
kubectl get deployments
```

### 4. Check running pods
```bash
kubectl get pods
```

### 5. Describe the deployment
```bash
kubectl describe deployment httpd
```

## Understanding the Components

 - Deployment – Manages the lifecycle of Pods and ensures the desired number of replicas are running.
 - Pod Template – Defines the container specifications (image, ports, labels, etc.).
 - ReplicaSet – Automatically created by the Deployment to manage Pods.
 - Container Image – Uses the httpd:latest image, which runs an Apache HTTP server.

