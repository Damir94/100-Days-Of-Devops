# 100 Days of Devops - Day 61: Kubernetes Init Containers Deployment — ic-deploy-nautilus

## Overview
This project demonstrates how to use Init Containers in Kubernetes to perform pre-deployment configuration tasks that cannot be handled directly inside the main container image.
The Nautilus DevOps team implemented this example to automate setup steps before launching an application container in the Kubernetes cluster.

The setup includes:
  - Creating a Kubernetes Deployment named ic-deploy-nautilus.
  - Using an Init Container to perform configuration before the main app runs.
  - Sharing data between containers using a volume (emptyDir).

## Objective
Before certain applications can run, some configuration files or data must be generated dynamically at runtime.
These changes cannot be baked into the image, so we use Init Containers — lightweight containers that run and complete their task before the main application container starts.

## Deployment Details
1. Deployment Configuration
  - Name: ic-deploy-nautilus
  - Replicas: 1
  - Labels:
  ```yam
    app: ic-nautilus
```
2. Init Container
The Init Container is responsible for preparing a message file inside a shared volume before the main container starts.
```yaml
initContainers:
  - name: ic-msg-nautilus
    image: fedora:latest
    command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/beta"]
    volumeMounts:
      - name: ic-volume-nautilus
        mountPath: /ic
```

Explanation:
  - Uses the fedora:latest image.
  - Executes a bash command that writes a message into /ic/beta.
  - Mounts a shared volume (/ic) so the main container can read the same file.

3. Main Container
The main application container continuously reads and prints the content written by the init container.
```yaml
containers:
  - name: ic-main-nautilus
    image: fedora:latest
    command: ["/bin/bash", "-c", "while true; do cat /ic/beta; sleep 5; done"]
    volumeMounts:
      - name: ic-volume-nautilus
        mountPath: /ic
```
Explanation:
  - Runs an infinite loop printing the contents of /ic/beta.
  - Shares the same /ic directory with the init container via the shared volume.

4. Shared Volume
```yaml
volumes:
  - name: ic-volume-nautilus
    emptyDir: {}
```
Explanation:
  - emptyDir provides temporary storage that exists only for the pod’s lifetime.
  - Both containers share this volume, allowing data exchange between init and main containers.

## How It Works
1. When the pod starts, the Init Container runs first.
2. It writes a welcome message into /ic/beta.
3. After the init container finishes successfully, the main container starts.
4. The main container reads and prints the message repeatedly, showing that the initialization was successful.

## Deployment YAML
Here’s the complete manifest used in this project:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-nautilus
  template:
    metadata:
      labels:
        app: ic-nautilus
    spec:
      initContainers:
        - name: ic-msg-nautilus
          image: fedora:latest
          command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/beta"]
          volumeMounts:
            - name: ic-volume-nautilus
              mountPath: /ic
      containers:
        - name: ic-main-nautilus
          image: fedora:latest
          command: ["/bin/bash", "-c", "while true; do cat /ic/beta; sleep 5; done"]
          volumeMounts:
            - name: ic-volume-nautilus
              mountPath: /ic
      volumes:
        - name: ic-volume-nautilus
          emptyDir: {}
```
## Commands to Deploy
```bash
# Apply the deployment
kubectl apply -f ic-deploy-nautilus.yaml

# Check the deployment status
kubectl get deployments

# Verify running pods
kubectl get pods

# View pod logs to see init container message
kubectl logs <pod-name> -f
```

Expected output:
```css
Init Done - Welcome to xFusionCorp Industries
```

<img width="1567" height="524" alt="Screenshot 2025-10-23 at 9 37 57 PM" src="https://github.com/user-attachments/assets/b606d93c-d0bf-49c9-88b6-60ec558a613b" />

## Key Learnings
  - Init Containers help with pre-deployment setup before the main application starts.
  - emptyDir volumes allow data sharing between containers in the same Pod.
  - Sequential execution: The main container starts only after the init container completes successfully.
  - Useful for initializing configurations, database schema setup, or environment preparation.

## Conclusion
This task demonstrates how to use init containers effectively to prepare environments dynamically inside Kubernetes Pods.
It’s a practical and powerful approach for DevOps teams to handle pre-deployment customization when building flexible and production-ready systems.
