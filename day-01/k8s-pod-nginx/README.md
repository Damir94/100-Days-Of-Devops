# Kubernetes Pod Deployment — httpd Example

## Overview

The Nautilus DevOps team is exploring Kubernetes to manage and deploy containerized applications efficiently.
As part of this journey, one of our tasks involved creating and deploying a simple Apache HTTP Server (httpd) pod to understand Kubernetes fundamentals like Pods, Containers, and Labels.
This document explains the task details, YAML configuration, and commands used.

## Task Objective

Create a Kubernetes Pod with the following specifications:
 - Pod Name: pod-httpd
 - Container Image: httpd:latest
 - Container Name: httpd-container
 - Label: app: httpd_app

## YAML Manifest
Below is the YAML file (pod-httpd.yaml) used to create the pod:

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

## Explanation:

| Field             | Description                                                       |
| ----------------- | ----------------------------------------------------------------- |
| `apiVersion`      | Specifies the Kubernetes API version used to create the resource. |
| `kind`            | Defines the type of Kubernetes object (here it’s a Pod).          |
| `metadata.name`   | The name assigned to the Pod (`pod-httpd`).                       |
| `metadata.labels` | Key-value labels used to identify and organize resources.         |
| `spec.containers` | Defines the containers running inside the Pod.                    |
| `name`            | The container name (`httpd-container`).                           |
| `image`           | The Docker image used (`httpd:latest`).                           |


## Deployment Steps

### 1. Create the YAML file
```bash
vi pod-httpd.yaml
```
Paste the YAML content above and save.

### 2. Deploy the Pod
```bash
kubectl apply -f pod-httpd.yaml
```

### 3. Verify Pod Creation
```bash
kubectl get pods
```

### 4. Describe Pod Details
```bash
kubectl describe pod pod-httpd
```

## Key Takeaways
 - Pods are the smallest deployable units in Kubernetes — they encapsulate one or more containers.
 - Labels make it easy to organize and filter Kubernetes resources.
 - The httpd:latest image allows serving web content quickly within a Kubernetes cluster.
 - YAML manifests provide declarative configuration for repeatable, version-controlled deployments.

## Conclusion
This task was a foundational step in our Kubernetes learning path.
We successfully deployed an Apache HTTP Server pod, applied labels for organization, and practiced managing it through kubectl commands.

<img width="1274" height="567" alt="Screenshot 2025-10-08 at 12 24 54 PM" src="https://github.com/user-attachments/assets/0ebf6cf0-9bc7-440c-a626-e2a6a39114cf" />


