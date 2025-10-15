# 100 Days of Devops - Day 52: Kubernetes Deployment Rollback — nginx-deployment

## Overview
This document explains the procedure followed by the Nautilus DevOps team to roll back a Kubernetes deployment to a previous version.
Earlier today, a new release of the application was deployed. However, a customer reported a bug related to this release.
To restore service stability, we reverted the deployment nginx-deployment to its previous revision.

### Prerequisites
Before performing a rollback, ensure that:
 - You have kubectl installed and configured to access the cluster.
 - You have the necessary permissions to modify deployments.
 - You can identify the affected deployment (in this case, nginx-deployment).

## Rollback Procedure
1. Check the Current Deployment Status
Before rolling back, confirm the current state of the deployment:
```bash
kubectl get deployments
kubectl describe deployment nginx-deployment
```

2. View Deployment Revision History
To see all available revisions of the deployment:
```bash
kubectl rollout history deployment/nginx-deployment
```
This command lists all previous versions (revisions) of the deployment, helping you identify which one to roll back to if needed.

3. Roll Back to the Previous Revision
To revert to the last working version:
```bash
kubectl rollout undo deployment/nginx-deployment
```
This command automatically restores the deployment to its most recent stable revision.

If you need to roll back to a specific version:
```bash
kubectl rollout undo deployment/nginx-deployment --to-revision=<revision_number>
```
Replace <revision_number> with the desired revision obtained from the history command.

4. Verify the Rollback
After performing the rollback, verify the rollout status:
```bash
kubectl rollout status deployment/nginx-deployment
```
You should see output similar to:
```csharp
deployment "nginx-deployment" successfully rolled out
```
To confirm the deployment is running the expected version:
```bash
kubectl get pods -l app=nginx
```
<img width="1780" height="582" alt="Screenshot 2025-10-15 at 9 31 48 AM" src="https://github.com/user-attachments/assets/39a14000-b1e5-4a7c-a5c4-833ca2303f95" />

## Notes
 - Each time a deployment is updated, Kubernetes creates a new revision.
 - The rollout undo command works only if previous revisions are stored in the ReplicaSet history.
 - Keeping a consistent naming and versioning pattern for container images helps track versions easily.

## Outcome
After executing the rollback, the nginx-deployment was successfully reverted to its previous stable release, 
restoring normal service operations and resolving the issue reported by the customer.
