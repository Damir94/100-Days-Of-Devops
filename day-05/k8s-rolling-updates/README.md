# Nautilus DevOps Task: Kubernetes Rolling Update

## Overview

The Nautilus application development team introduced updates to the nginx web server application. 
The DevOps team performed a rolling update to deploy these changes safely on the Kubernetes cluster without downtime.
 - Deployment Name: nginx-deployment
 - Container Image: Updated to nginx:1.19
 - Purpose: Apply updates while keeping the application available and ensuring all pods are operational.

### Task Details
1. Verify Current Deployment
Before updating, we checked the existing deployment and pods:
```bash
kubectl get deployments nginx-deployment
kubectl get pods -l app=nginx
```
This ensures we know the current state of the application and its running pods.

2. Perform Rolling Update
The deployment was updated to the new image using:
```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.19
```
Replace nginx-container with the actual container name from the deployment.
This triggers Kubernetes to gradually replace old pods with new ones, ensuring zero downtime.

3. Monitor the Rollout
To verify that the update is proceeding correctly:
```bash
kubectl rollout status deployment/nginx-deployment
```
Output confirms the successful deployment:
```csharp
deployment "nginx-deployment" successfully rolled out
```

4. Verify Pods
Finally, we checked that all pods are running the updated image:
```bash
kubectl get pods -l app=nginx
kubectl describe pod <pod-name> | grep Image
```
All pods should be in the Running state and using nginx:1.19.

5. Rollback (if needed)
In case of issues, the update can be reverted:
```bash
kubectl rollout undo deployment/nginx-deployment
```
## Outcome
 - All nginx pods were updated successfully to nginx:1.19.
 - Application remained available during the update.
 - Ensured stable and reliable deployment with Kubernetes rolling update strategy.
