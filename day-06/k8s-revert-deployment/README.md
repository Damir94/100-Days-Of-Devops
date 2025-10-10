# Rolling Back a Kubernetes Deployment

## Overview
Earlier today, the Nautilus DevOps team deployed a new release for the nginx-deployment application. 
Shortly after the deployment, a customer reported a bug related to the new release. 
To ensure application stability and minimize impact, the team decided to rollback the deployment to the previous version.

## Objective
 - Revert the nginx-deployment to its previous revision.
 - Ensure all pods are running correctly after the rollback.
 - Confirm the application is stable and functioning as expected.

## Steps Performed

1. Check Deployment Rollout History.
To see all revisions of the deployment:
```bash
kubectl rollout history deployment/nginx-deployment
```
Output example:
```css
REVISION  CHANGE-CAUSE
1         Initial deployment
2         Updated to nginx:1.19
```

2. Rollback to the Previous Revision.
Revert the deployment to the previous working version:
```bash
kubectl rollout undo deployment/nginx-deployment
```

3. Verify the Rollback.
Check rollout status:
```bash
kubectl rollout status deployment/nginx-deployment
```
Confirm the container image has reverted:
```bash
kubectl get deployment nginx-deployment -o=jsonpath='{.spec.template.spec.containers[0].image}'
```
The output should show the previous image version, ensuring the rollback succeeded.

## Outcome
 - The deployment was successfully reverted to the previous version.
 - All pods are running normally with no downtime.
 - The reported bug related to the new release is no longer affecting the application.

## Key Takeaways
 - Kubernetes makes it simple to rollback deployments safely.
 - Always monitor application stability and pod health after a rollback.
 - Maintaining deployment history ensures quick recovery in case of failed releases.
