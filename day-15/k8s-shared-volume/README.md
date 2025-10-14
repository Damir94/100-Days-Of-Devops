# Sharing Volumes Between Containers in a Kubernetes Pod

## Overview
In this guide, we’ll demonstrate how to create a Kubernetes Pod that runs two containers sharing a common volume. 
This setup allows both containers to access the same files, making it useful for temporary data exchange, caching, or logs within a Pod.
The DevOps team often uses this pattern when multiple containers within a single Pod need to communicate through files or share data temporarily.

## Task Summary
We will:
 - Create a Pod named volume-share-devops.
 - Use two containers, both running the fedora:latest image.
 - Mount a shared emptyDir volume between them.
 - Create a file in one container and verify it appears in the other.

## Pod Specification
YAML Manifest: volume-share-devops.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-devops
spec:
  containers:
    - name: volume-container-devops-1
      image: fedora:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/news

    - name: volume-container-devops-2
      image: fedora:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/apps

  volumes:
    - name: volume-share
      emptyDir: {}
```
## Explanation:

| Component         | Description                                                                                                     |
| ----------------- | --------------------------------------------------------------------------------------------------------------- |
| **emptyDir**      | Creates a temporary directory shared among containers in the same Pod. It gets deleted once the Pod is removed. |
| **volumeMounts**  | Defines where the shared volume will be mounted inside each container.                                          |
| **sleep command** | Keeps both containers running to allow testing and inspection.                                                  |


## Steps to Deploy
1. Create the Pod
```bash
kubectl apply -f volume-share-devops.yaml
```
2. Check if the Pod is running:
```bash
kubectl get pods
```
3. Create a File in the First Container
Exec into the first container:
```bash
kubectl exec -it volume-share-devops -c volume-container-devops-1 -- /bin/bash
```
Create a test file:
```bash
echo "Shared file created by container 1" > /tmp/news/news.txt
cat /tmp/news/news.txt
```
You should see:
```vbnet
Shared file created by container 1
```
Exit:
```bash
exit
```
4. Verify the File in the Second Container
Exec into the second container:
```bash
kubectl exec -it volume-share-devops -c volume-container-devops-2 -- /bin/bash
```
Check for the shared file:
```bash
cat /tmp/apps/news.txt
```
## Expected output:
```vbnet
Shared file created by container 1
```
This confirms both containers share the same volume.

## Why Use emptyDir?
 - Fast and simple: Data stays on the node’s local storage.
 - Temporary storage: Ideal for caching or passing temporary files.
 - Pod-scoped: When the Pod is deleted, data is automatically cleaned up.
Use cases include:
 - Sharing logs between app and sidecar containers
 - Writing temporary build or cache files
 - Enabling lightweight data communication within the same Pod

## Cleanup
Once testing is complete:
```bash
kubectl delete pod volume-share-devops
```

## Summary

| Task          | Result                                             |
| ------------- | -------------------------------------------------- |
| Pod Name      | `volume-share-devops`                              |
| Container 1   | `volume-container-devops-1`                        |
| Container 2   | `volume-container-devops-2`                        |
| Shared Volume | `emptyDir` (`volume-share`)                        |
| Mounted Paths | `/tmp/news` and `/tmp/apps`                        |
| Verification  | File created in one container visible in the other |
