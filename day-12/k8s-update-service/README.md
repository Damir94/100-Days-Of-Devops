# Updating the Nautilus Application on Kubernetes

The Nautilus DevOps team successfully updated the nginx application deployed on our Kubernetes cluster. 
This update was carried out without deleting the existing deployment or service, ensuring zero downtime. 
Below is a detailed overview of the changes and the steps followed.

## Background
Our existing setup included:

 - Deployment: nginx-deployment
 - Service: nginx-service
 - Current State:
     - Replicas: 1
     - Container Image: nginx:1.19
     - NodePort: 30008

The goal was to update the application with new features developed by the Nautilus application team.

## Changes Implemented

1. Service NodePort Update
 - Previous NodePort: 30008
 - Updated NodePort: 32165
 - Reason: To meet updated networking requirements and avoid port conflicts.

2. Deployment Scaling
 - Previous Replicas: 1
 - Updated Replicas: 5
 - Reason: Increase application availability and handle higher traffic.

3. Container Image Update
 - Previous Image: nginx:1.19
 - Updated Image: nginx:latest
 - Container Name: nginx-container
 - Reason: Incorporate the latest features and security patches.

## Implementation Steps

1. Update Service NodePort
```bash
kubectl patch service nginx-service \
  -p '{"spec":{"ports":[{"port":80,"targetPort":80,"nodePort":32165}]}}'
```

2. Scale Deployment Replicas
```bash
kubectl scale deployment nginx-deployment --replicas=5
```
3. Update Deployment Image
```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:latest
```

4. Verify Changes
```bash
kubectl rollout status deployment/nginx-deployment
kubectl get deployment nginx-deployment -o wide
kubectl get service nginx-service -o wide
kubectl get pods -l app=nginx
```

## Results

The nginx-service NodePort was successfully updated to 32165.

The nginx-deployment now runs 5 replicas, improving redundancy and availability.

The container image was updated to nginx:latest, ensuring the latest features and security patches are applied.

The application continues to run without downtime, ensuring smooth service for users.

Key Takeaways
 - Kubernetes allows updates to services and deployments without deletion, which is crucial for production systems.
 - Scaling replicas and updating container images can be done seamlessly with kubectl.
 - Always verify container names when updating images to avoid errors like unable to find container.
