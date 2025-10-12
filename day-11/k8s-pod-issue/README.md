# Kubernetes Pod Deployment – Nautilus DevOps Team

## Overview

The Nautilus DevOps team recently completed a task to deploy a web application on a Kubernetes cluster. 
The goal was to create a pod with multiple containers and ensure the application runs smoothly and is accessible.
This documentation outlines the steps taken, issues encountered, and resolutions applied.

## Pod Details
Pod Name: webserver
Containers:
 - nginx-container → nginx:latest (web server)
 - sidecar-container → ubuntu:latest (sidecar for auxiliary tasks)

## Step 1: Creating the Pod
We defined a pod YAML file with the required containers. The nginx-container runs the web application, 
and the sidecar-container runs a long-lived process to avoid crashing.

## Example Pod YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
    - name: sidecar-container
      image: ubuntu:latest
      command: ["sleep", "infinity"]
```

## Step 2: Deploying the Pod
Deploy the pod using:
```bash
kubectl apply -f webserver-pod.yaml
```
Verify the pod status:
```bash
kubectl get pods
kubectl describe pod webserver
```

## Step 3: Troubleshooting
During deployment, the following issues were encountered:

1. ErrImagePull
 - Cause: Typo in the image name (nginx:latests)
 - Resolution: Corrected the image to nginx:latest in the YAML file and reapplied.

2. Sidecar CrashLoopBackOff
 - Cause: Sidecar container (ubuntu:latest) had no running command and exited immediately
 - Resolution: Added command: ["sleep", "infinity"] to keep the container alive

## Step 4: Verification
After fixing the issues:
```bash
kubectl get pods
```
Pod status: Running

Nginx container logs:
```bash
kubectl logs webserver -c nginx-container
```
Application accessible on container port 80

## Key Takeaways
 - Always double-check image names and tags to avoid ErrImagePull.
 - Sidecar containers require a long-running process to prevent crashes.
 - Use kubectl describe pod and kubectl logs for troubleshooting container issues.
