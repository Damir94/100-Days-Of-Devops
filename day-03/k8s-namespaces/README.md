# Nautilus DevOps — Kubernetes Namespace and Pod Setup

## Overview
This document explains the steps followed by the Nautilus DevOps Team to set up a new Kubernetes namespace and deploy an Nginx Pod inside it.
This setup is part of the initial environment preparation for deploying microservices on the Kubernetes platform.

## Task Objective
The goal of this task was to:

1. Create a new namespace called dev.
2. Deploy a Pod named dev-nginx-pod within that namespace.
3. Use the Nginx image with the latest tag (nginx:latest).

## Implementation Steps

### Step 1: Create a Namespace
Namespaces in Kubernetes help logically separate environments or teams within a cluster.
```bash
kubectl create namespace dev
```
This command creates a namespace named dev where all related resources for development can be organized.

### Step 2: Deploy an Nginx Pod in the Namespace
To deploy a pod named dev-nginx-pod using the nginx:latest image:
```bash
kubectl run dev-nginx-pod \
  --image=nginx:latest \
  --restart=Never \
  -n dev
```
Command Breakdown:
 - --image=nginx:latest → Pulls the latest version of the Nginx image.
 - --restart=Never → Ensures this is a Pod, not a Deployment or ReplicaSet.
 - -n dev → Specifies the namespace where the Pod should be created.

### Step 3: Verify the Deployment
To confirm that the pod is running successfully inside the dev namespace:
```bash
kubectl get pods -n dev
```
Expected output:
```sql
NAME             READY   STATUS    RESTARTS   AGE
dev-nginx-pod    1/1     Running   0          10s
```

## Alternative YAML Configuration
You can also achieve the same result using a Kubernetes manifest file:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
---
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev
spec:
  containers:
  - name: nginx
    image: nginx:latest
```
Apply the manifest:
```bash
kubectl apply -f pod.yaml
```

## Validation
To ensure everything is working correctly:
```bash
kubectl describe pod dev-nginx-pod -n dev
kubectl logs dev-nginx-pod -n dev
```
These commands display detailed information about the pod and confirm that Nginx is running properly.

## Conclusion
The Nautilus DevOps team successfully:

 - Created a dedicated development namespace (dev)
 - Deployed an Nginx Pod (dev-nginx-pod) using the latest image
 - Verified that the setup is running as expected

This setup provides a foundation for deploying additional microservices in isolated Kubernetes environments going forward.

<img width="1209" height="543" alt="Screenshot 2025-10-09 at 8 06 48 AM" src="https://github.com/user-attachments/assets/2fc9edcb-9542-4f96-b9ef-d0733d17fd5f" />
