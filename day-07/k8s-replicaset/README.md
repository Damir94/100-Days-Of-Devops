# Deploying an nginx ReplicaSet on Kubernetes

The Nautilus DevOps team successfully created and deployed an nginx ReplicaSet on our Kubernetes 
cluster to ensure high availability and load distribution for our front-end application.

## Objective
The goal of this task was to:
 - Deploy multiple instances of an nginx container.
 - Ensure the application remains available even if individual pods fail.
 - Apply proper labeling for easier management and selection.

## Implementation Details
ReplicaSet Specifications:
 - ReplicaSet Name: nginx-replicaset
 - Container Name: nginx-container
 - Image Used: nginx:latest
 - Replica Count: 4
 - Labels:
    - app: nginx_app
    - type: front-end
The ReplicaSet was configured to manage 4 replicas of the nginx container, ensuring redundancy and high availability.

## Kubernetes Manifest
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 4
  selector:
    matchLabels:
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
        ports:
        - containerPort: 80
```
## Deployment Steps

1. Save the manifest as nginx-replicaset.yaml.

2. Apply the ReplicaSet to the cluster:
```bash
kubectl apply -f nginx-replicaset.yaml
```

3. Verify the deployment:
```bash
kubectl get rs
kubectl get pods -l app=nginx_app
```
All 4 pods should be in Running state, managed by the nginx-replicaset.

## Outcome
 - Successfully deployed 4 nginx pods under a single ReplicaSet.
 - Each pod is labeled for easy selection and management.
 - High availability is ensured; pods will automatically be recreated if any instance fails.
