# HTTPD Deployment Test – Rolling Update & Rollback (Dev Environment)

## Overview
The Nautilus DevOps team planned a production deployment for next week.
Before going live, the team wanted to test the rolling update and rollback process in the Dev environment to identify potential risks early.
This README documents the steps performed to deploy, update, and rollback an HTTPD application running on Kubernetes.

## Objectives
 - Create a dedicated Kubernetes namespace for isolation.
 - Deploy the HTTPD application (version 2.4.28) with multiple replicas.
 - Expose the deployment using a NodePort service.
 - Perform a rolling update to a newer version (2.4.43).
 - Test and perform a rollback to the original version.

## Kubernetes Configuration Details
| Resource Type | Name            | Namespace  | Image Version                   | Replicas | Update Strategy                                  | NodePort |
| ------------- | --------------- | ---------- | ------------------------------- | -------- | ------------------------------------------------ | -------- |
| Deployment    | `httpd-deploy`  | `nautilus` | `httpd:2.4.28` → `httpd:2.4.43` | 4        | RollingUpdate (`maxSurge=1`, `maxUnavailable=2`) | `30008`  |
| Service       | `httpd-service` | `nautilus` | -                               | -        | -                                                | `30008`  |


## Step-by-Step Implementation
1. Create Namespace
```bash
kubectl create namespace nautilus
```

2. Create Deployment
Create file httpd-deploy.yaml:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deploy
  namespace: nautilus
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 2
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
          image: httpd:2.4.28
          ports:
            - containerPort: 80

```

3. Apply the deployment:
```bash
kubectl apply -f httpd-deploy.yaml
```

4. Expose the Deployment via NodePort Service
Create file httpd-service.yaml:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
  namespace: nautilus
spec:
  type: NodePort
  selector:
    app: httpd
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008

```

5. Apply the service:
```bash
kubectl apply -f httpd-service.yaml
```
6. Verify the Deployment and Service
```bash
kubectl get deployments -n nautilus
kubectl get pods -n nautilus
kubectl get svc -n nautilus
```
Expected result:
 - 4 pods running with image httpd:2.4.28
 - Service httpd-service available on NodePort 30008

7. Perform Rolling Update to New Version
Update image:
```bash
kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 -n nautilus
```
Monitor rollout:
```bash
kubectl rollout status deployment/httpd-deploy -n nautilus
```
Confirm new image version:
```bash
kubectl describe deployment httpd-deploy -n nautilus | grep Image
```
Should show:
```makefile
Image: httpd:2.4.43
```
8. Rollback to Previous Version
Rollback to the previous version:
```bash
kubectl rollout undo deployment/httpd-deploy -n nautilus
```
Check status and confirm:
```bash
kubectl rollout status deployment/httpd-deploy -n nautilus
kubectl describe deployment httpd-deploy -n nautilus | grep Image
```
Should revert to:
```makefile
Image: httpd:2.4.28
```
<img width="1751" height="819" alt="Screenshot 2025-10-15 at 9 46 28 AM" src="https://github.com/user-attachments/assets/5f280dc5-fef6-43bb-8cc4-dba3f4c5f439" />

## What We Learned
 - Rolling updates allow zero-downtime upgrades.
 - Rollback is quick and reliable when something goes wrong.
 - Proper configuration of maxSurge and maxUnavailable ensures smooth transitions.
 - Always test deployment strategies in a non-production environment first.

## Summary
| Action           | Command                                                                    |
| ---------------- | -------------------------------------------------------------------------- |
| Create Namespace | `kubectl create namespace nautilus`                                        |
| Apply Deployment | `kubectl apply -f httpd-deploy.yaml`                                       |
| Apply Service    | `kubectl apply -f httpd-service.yaml`                                      |
| Update Version   | `kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 -n nautilus` |
| Rollback         | `kubectl rollout undo deployment/httpd-deploy -n nautilus`                 |
