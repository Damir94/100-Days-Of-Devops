# Deploying an Nginx ReplicationController in Kubernetes

## Overview

The Nautilus DevOps team aims to create a highly available infrastructure for hosting web applications. 
To achieve this, we are using a ReplicationController in Kubernetes that 
ensures a specified number of pod replicas are always running.

This setup automatically replaces any failed or terminated pod, 
providing resilience and reliability for the deployed application.

## Task Objective
We will deploy an Nginx ReplicationController that:
 - Uses the nginx:latest image
 - Runs 3 replicas (pods) for high availability
 - Labels all pods with:
    - app: nginx_app
    - type: front-end

 - Names the ReplicationController as nginx-replicationcontroller
 - Ensures all pods are in a Running state post-deployment

## YAML Manifest
Below is the YAML configuration used to create the ReplicationController:
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-replicationcontroller
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 3
  selector:
    app: nginx_app
    type: front-end
  template:
    metadata:
      labels:
        app: nginx_app
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
```

## Deployment Steps
1. Save the Manifest File.
Save the above YAML as:
```bash
nginx-replicationcontroller.yaml
```

2. Apply the Configuration.
Run the following command to create the ReplicationController:
```bash
kubectl apply -f nginx-replicationcontroller.yaml
```
3. Verify the ReplicationController.
Check that it has been successfully created:
```bash
kubectl get rc
```

Expected output:
```sql
NAME                       DESIRED   CURRENT   READY   AGE
nginx-replicationcontroller   3         3         3      ...
```
4. Check the Pod Status.
Verify that all pods created by the ReplicationController are running:
```bash
kubectl get pods -l app=nginx_app
```

Example output:
```sql
NAME                                       READY   STATUS    RESTARTS   AGE
nginx-replicationcontroller-xxxxx           1/1     Running   0          2m
nginx-replicationcontroller-yyyyy           1/1     Running   0          2m
nginx-replicationcontroller-zzzzz           1/1     Running   0          2m
```
## Key Concepts

### ReplicationController
 - A ReplicationController (RC) ensures that a specified number of pod replicas are running at any given time. If a pod fails, the RC automatically replaces it.

### Labels and Selectors
 - Labels like app=nginx_app and type=front-end are used to identify and manage the pods that belong to the ReplicationController.

## Expected Outcome
After applying the configuration:
- Three nginx pods will be deployed.
- If any pod fails, the ReplicationController will automatically create a replacement.
- The application remains highly available and fault-tolerant.

## Next Steps
 - Expose the ReplicationController as a Service to make it accessible within or outside the cluster.
 - Consider upgrading to a Deployment for more flexible rolling updates and scaling in production environments.

