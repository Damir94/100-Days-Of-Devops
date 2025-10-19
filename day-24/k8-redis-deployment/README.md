# Redis Deployment on Kubernetes – Troubleshooting and Fixes

Overview
Last week, the Nautilus DevOps team deployed a Redis application on a Kubernetes cluster. 
The deployment was initially running smoothly. However, during routine modifications, 
the Redis pods went down. This documentation outlines the issues encountered, 
steps taken to troubleshoot, and solutions implemented to restore the application.

## Deployment Details
 - Deployment Name: redis-deployment
 - Application: Redis
 - Environment: Kubernetes cluster
 - Image Used: redis:alpine
 - ConfigMap Used: redis-config

## Issues Identified

1. ConfigMap Mount Failure
 - Error Message:
```rust
MountVolume.SetUp failed for volume "config" : configmap "redis-conig" not found
```
Cause: Typo in the ConfigMap name in the deployment YAML (redis-conig instead of redis-config).

2. Image Pull Failure
 - Error Message:
```pgsql
Failed to pull image "redis:alpin": docker.io/library/redis:alpin: not found
```
Cause: Typo in the Docker image tag (alpin instead of alpine).

## Troubleshooting Steps

1. Check Deployment and Pod Status
```bash
kubectl get deployment redis-deployment
kubectl get pods -l app=redis
```
Confirmed pods were failing and not in Running state.

2. Inspect Pod Details
```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name> --previous
```
Identified FailedMount and ErrImagePull errors.

3. Check ConfigMaps
```bash
kubectl get configmaps
```
Confirmed the correct ConfigMap name: redis-config.

## Solutions Implemented

1. Fix ConfigMap Reference
```bash
kubectl edit deployment redis-deployment
```
Updated the volume section in the YAML:
```yaml
volumes:
  - name: config
    configMap:
      name: redis-config
```
2. Fix Docker Image Tag
```bash
kubectl edit deployment redis-deployment
```
Corrected the container image:
```yaml
image: redis:alpine
```
## Verification

1. Rollout Status
```bash
kubectl rollout status deployment redis-deployment
```
2. Pod Status
```bash
kubectl get pods -l app=redis
```
3. Logs Check
```bash
kubectl logs <redis-pod-name>
```
Pods were running successfully, and Redis service was restored.

## Lessons Learned
 - Always double-check ConfigMap names and image tags before applying changes.
 - Inspect pod events and logs to quickly identify root causes.
 - Use rolling updates to safely apply fixes without downtime.
