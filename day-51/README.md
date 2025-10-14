# 100 Days of Devops - Day: 51 - Kubernetes Rolling Update — Nginx Deployment

## Overview
The Nautilus Application Development Team introduced updates to the Nginx-based application running in the Kubernetes cluster. 
To deploy the changes without downtime, a rolling update was performed on the nginx-deployment using the new nginx:1.17 image.
This document explains the steps taken, why they are important, and how to verify a successful rollout.

## Objective
 - Update the existing nginx-deployment to use the nginx:1.17 image.
 - Ensure all pods are updated gradually and remain operational.
 - Prevent downtime by leveraging Kubernetes rolling updates.

## Implementation Steps
1. Identify the Container Name
Before updating, find the container name inside the deployment:
```bash
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[*].name}'
```
This outputs the container name(s) in the deployment. Example: nginx-container.

2. Perform the Rolling Update
Use the container name to update the image:
```bash
kubectl set image deployment/nginx-deployment <container-name>=nginx:1.17
```

3. Monitor the Update
Check the rollout status to ensure all pods are updated successfully:
```bash
kubectl rollout status deployment/nginx-deployment
```
Expected output:
```csharp
deployment "nginx-deployment" successfully rolled out
```

4. Verify Updated Pods
List all pods for the deployment and confirm they are running the new image:
```bash
kubectl get pods
kubectl describe deployment nginx-deployment | grep Image
```
Output should show:
```makefile
Image: nginx:1.17
```

<img width="1587" height="886" alt="Screenshot 2025-10-13 at 8 35 16 PM" src="https://github.com/user-attachments/assets/d2e6772f-8170-4ad0-8d77-24ea07a1b267" />

5. Rollback (If Needed)
If something goes wrong during the rollout:
```bash
kubectl rollout undo deployment/nginx-deployment
```
This restores the deployment to the previous stable version.

## Understanding Rolling Updates
 - Kubernetes updates pods gradually instead of all at once.
 - Old pods are terminated only after new pods are running, preventing downtime.
 - kubectl rollout status monitors progress and ensures a safe update.

## Outcome
 - The nginx-deployment now runs the updated nginx:1.17 image.
 - All pods are operational post-update.
 - The rolling update ensures smooth deployment without affecting end-users.

## Next Steps
 - Apply similar rolling updates for other deployments whenever new application versions are released.
 - Enable readiness and liveness probes to further improve deployment reliability.
 - Monitor pod performance after updates using tools like Prometheus or Lens.

