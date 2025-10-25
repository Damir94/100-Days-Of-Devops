# 100 Days of Devops - Day 62: Kubernetes Secret and Pod Deployment — Nautilus DevOps Task

## Overview
The Nautilus DevOps team needed to securely store license information for tools deployed in a Kubernetes cluster.
To achieve this, we utilized Kubernetes Secrets — a built-in way to store and manage sensitive data such as passwords, tokens, and license keys.

In this task, we created:
1. A Kubernetes Secret named media that stores the license key from a file.
2. A Pod named secret-nautilus that consumes this secret and mounts it inside the container.

### Step 1: Creating the Secret
We were given a file /opt/media.txt containing a password or license number.
Kubernetes allows us to create a secret from this file using the following command:
```bash
kubectl create secret generic media --from-file=/opt/media.txt
```
### Explanation
  - kubectl create secret generic — creates a secret of type “generic”.
  - media — name of the secret.
  - --from-file — adds the content of a local file as a key-value pair inside the secret.
When created, Kubernetes automatically encodes the value in base64 for storage safety.

You can verify the secret creation using:
```bash
kubectl get secrets
kubectl describe secret media
```
### Step 2: Creating the Pod
We needed a pod that:

  - Uses the Fedora (latest) image.
  - Has a container named secret-container-nautilus.
  - Runs a sleep command to remain in the Running state.
  - Mounts the previously created secret under /opt/demo.

Here’s the YAML definition used:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-nautilus
spec:
  containers:
    - name: secret-container-nautilus
      image: fedora:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: media-secret
          mountPath: /opt/demo
  volumes:
    - name: media-secret
      secret:
        secretName: media
```
### Explanation
  - volumeMounts: Defines where the secret will be mounted inside the container.
  - volumes.secret.secretName: References the Kubernetes secret we created earlier.
  - sleep 3600: Keeps the pod alive for 1 hour for verification purposes.
We applied the YAML file using:
```bash
kubectl apply -f secret-pod.yaml
```
### Step 3: Verifying the Secret Inside the Pod
Once the pod was running, we verified that the secret was successfully mounted.
```bash
kubectl exec -it secret-nautilus -- /bin/bash
```
Then inside the container:
```bash
ls /opt/demo
cat /opt/demo/media.txt
```

<img width="1537" height="665" alt="Screenshot 2025-10-25 at 8 28 09 AM" src="https://github.com/user-attachments/assets/5252a405-89ca-4ec7-a3be-4ff087042827" />


You should see the content of the original media.txt file displayed — confirming that the secret was mounted successfully.

### Validation Checklist
Before final verification, ensure:
| Item             | Description                                   | Status |
| ---------------- | --------------------------------------------- | ------ |
| ✅ Secret created | `media` secret exists                         | ✔️     |
| ✅ Pod created    | `secret-nautilus` is running                  | ✔️     |
| ✅ Secret mounted | `/opt/demo/media.txt` accessible in container | ✔️     |

### Key Takeaways
  - Kubernetes Secrets are the preferred way to store sensitive configuration data.
  - Secrets can be mounted as volumes or consumed as environment variables.
  - Encoding (base64) does not mean encryption — for production use, consider integrating KMS or sealed-secrets for stronger protection.
  - Always avoid hardcoding sensitive data in YAML files or container images.

### Useful Commands Reference
| Command                                                          | Description               |
| ---------------------------------------------------------------- | ------------------------- |
| `kubectl create secret generic media --from-file=/opt/media.txt` | Create the secret         |
| `kubectl apply -f secret-pod.yaml`                               | Deploy the pod            |
| `kubectl get pods`                                               | Check pod status          |
| `kubectl exec -it secret-nautilus -- /bin/bash`                  | Enter the pod             |
| `cat /opt/demo/media.txt`                                        | Verify the mounted secret |

