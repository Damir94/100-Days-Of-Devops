# Nautilus DevOps Kubernetes Tasks Documentation

## Overview
This documentation summarizes the recent tasks completed by the Nautilus DevOps team to improve the performance and reliability of Kubernetes-hosted applications. 
These tasks focused on implementing resource constraints to prevent pods from over-consuming cluster resources.

## Task: Create a Pod with Resource Requests and Limits

### Objective
To address performance issues caused by resource contention, we created a pod with specific CPU and memory requests and limits. 
This ensures that Kubernetes schedules the pod appropriately and prevents it from exceeding resource thresholds.

### Pod Details
 - Pod Name: httpd-pod
 - Container Name: httpd-container
 - Container Image: httpd:latest
 - Resource Requests:
    - CPU: 100m
    - Memory: 15Mi
 - Resource Limits:
    = CPU: 100m
   Memory: 20Mi

### YAML Manifest
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
      resources:
        requests:
          memory: "15Mi"
          cpu: "100m"
        limits:
          memory: "20Mi"
          cpu: "100m"
```

## How it works:
 - requests: This is what the container is guaranteed to get from the node. Kubernetes uses this for scheduling decisions.
 - limits: This is the maximum the container is allowed to use. If it tries to exceed this, it may be throttled (CPU) or killed (memory).

## Steps to Deploy

### 1. Save the YAML manifest as httpd-pod.yaml.

### 2. Deploy the pod to the Kubernetes cluster:
```bash
kubectl apply -f httpd-pod.yaml
```

### 3. Verify the pod is running:
```bash
kubectl get pods
```

### 4. Check detailed resource configuration:
```bash
kubectl describe pod httpd-pod
```

## Key Benefits
 - Ensures the pod receives guaranteed minimum resources (requests) to run reliably.
 - Prevents the container from consuming excessive resources (limits), protecting other workloads in the cluster.
 - Helps mitigate performance issues in multi-tenant or resource-constrained environments.



