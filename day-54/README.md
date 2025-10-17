# 100 Day of Devops - Day 54: Kubernetes Shared Volume — Multi-Container Pod
## Overview
This document describes how the Nautilus DevOps Team created a Kubernetes Pod with multiple containers sharing a common volume.
The goal was to simulate a real-world scenario where two containers within the same Pod need to share temporary data. This was achieved using an emptyDir volume type in Kubernetes.

## Objective
 - Create a Pod named volume-share-xfusion with:
 - Two containers (volume-container-xfusion-1 and volume-container-xfusion-2)
 - Both using the debian:latest image
 - A shared volume named volume-share mounted at different paths:
     - /tmp/media in the first container
     - /tmp/apps in the second container
Both containers run a simple sleep command to stay active.

## Pod Specification
Below is the Kubernetes YAML manifest used to create the Pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-xfusion
spec:
  containers:
    - name: volume-container-xfusion-1
      image: debian:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/media

    - name: volume-container-xfusion-2
      image: debian:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/apps

  volumes:
    - name: volume-share
      emptyDir: {}
```
## Steps Performed
1. Create the Pod
```bash
kubectl apply -f volume-share-xfusion.yaml
```
Verify that the Pod is running:
```bash
kubectl get pods
```

2. Test the Shared Volume
Step 1: Access the first container:
```bash
kubectl exec -it volume-share-xfusion -c volume-container-xfusion-1 -- bash
```
Create a test file:
```bash
echo "Shared volume test" > /tmp/media/media.txt
exit
```

Step 2: Access the second container:
```bash
kubectl exec -it volume-share-xfusion -c volume-container-xfusion-2 -- bash
```
Verify that the file exists and contains the same content:
```bash
cat /tmp/apps/media.txt
```
The file media.txt is accessible from both containers, confirming the shared volume works correctly.

<img width="1674" height="479" alt="Screenshot 2025-10-16 at 9 30 16 PM" src="https://github.com/user-attachments/assets/4827eab6-892c-4d4c-9c85-4b6bcaeb9749" />

## Key Takeaways
- emptyDir volumes provide temporary shared storage between containers within the same Pod.
- Data persists as long as the Pod is running, but is deleted when the Pod is removed.
- Ideal for use cases such as caching, temporary data sharing, or inter-container communication.

## Cleanup
To delete the Pod after testing:
```bash
kubectl delete pod volume-share-xfusion
```

## Outcome
The volume-share-xfusion Pod successfully demonstrated shared volume functionality in Kubernetes.
Both containers could read and write to the same temporary storage, 
simulating a real-world DevOps environment where multiple application components share intermediate data.
