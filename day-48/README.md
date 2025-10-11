# 100 Days 0f Devops - Day 48: Creating an HTTPD Pod in Kubernetes 

## Overview
The Nautilus DevOps team is exploring Kubernetes for containerized application management.
As part of the learning and setup process, one of the tasks involves creating a Pod that runs an Apache HTTPD web server inside a Kubernetes cluster.
This document explains the purpose, configuration, and verification steps for deploying the pod-httpd.

## Task Details

### Objective:
Create a Kubernetes Pod using the httpd image with the latest tag.

### Requirements:
 - Pod Name: pod-httpd
 - Container Name: httpd-container
 - Image: httpd:latest
 - Label: app=httpd_app

### YAML Manifest
Below is the manifest file used to define the Pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
```

### Deployment Steps
1. Create the YAML file
```bash
vi pod-httpd.yaml
```
Paste the manifest above and save the file.

2. Apply the configuration
```bash
kubectl apply -f pod-httpd.yaml
```

3. Verify Pod status
```bash
kubectl get pods
```

4. Inspect Pod details (optional)
```
kubectl describe pod pod-httpd
```

5. Access container logs (optional)
```bash
kubectl logs pod-httpd
```

## Expected Outcome
After successful deployment:
 - The Pod pod-httpd should be in the Running state.
 - The container httpd-container will be running the Apache HTTP Server (httpd).
 - The Pod will have the label app=httpd_app, which can be used later for selectors or grouping in Kubernetes services or deployments.

## Key Learning
This task demonstrates: 
 - How to create a basic Pod manifest in Kubernetes.
 - How to use labels for organizing workloads.
 - How container images are defined and run inside Kubernetes Pods.

