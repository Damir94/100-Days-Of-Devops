# Kubernetes Secrets Deployment — Nautilus DevOps Project
This documentation explains how the Nautilus DevOps team securely stores license or password information in Kubernetes using Kubernetes Secrets, and mounts them inside a running Pod.

## Overview

The team needed to deploy licensed tools inside a Kubernetes cluster. Since license keys are sensitive data, 
Kubernetes Secrets are used to store this information securely and make it available to applications when needed.

We’ll walk through the process of:

1. Creating a Kubernetes Secret from a file
2. Creating a Pod that mounts the Secret
3. Verifying that the Secret is accessible within the Pod

## Step 1: Create the Secret
The file /opt/official.txt contains the license or password value.
We create a generic secret from this file:
```bash
kubectl create secret generic official --from-file=/opt/official.txt
```
## Explanation
  - kubectl create secret generic — creates a generic type of secret.
  - official — the name of the secret.
  - --from-file — reads the key-value pair from the given file.
  - The contents of official.txt will be stored securely as a secret in Kubernetes.

To confirm that the secret was created:
```bash
kubectl get secrets
kubectl describe secret official
```
Note: The secret data is base64-encoded, not encrypted.
Kubernetes manages the encryption if configured at the cluster level.

## Step 2: Create the Pod Manifest
Create a YAML file named secret-pod.yaml and add the following:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-devops
spec:
  containers:
    - name: secret-container-devops
      image: debian:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: secret-volume
          mountPath: /opt/games
  volumes:
    - name: secret-volume
      secret:
        secretName: official
```
Explanation of Each Section

#### metadata
  - name: secret-devops → Assigns the Pod a name.

#### containers
  - name: secret-container-devops → Container name.
  - image: debian:latest → Uses the Debian image.
  - command: ["sleep", "3600"] → Keeps the container running for one hour (or indefinitely if adjusted).
  - volumeMounts → Defines where the secret will be mounted inside the container (/opt/games).

#### volumes
  - name: secret-volume → Logical name for the mounted volume.
  - secret.secretName: official → References the secret we created earlier (official).

Apply the manifest:
```bash
kubectl apply -f secret-pod.yaml
```
## Step 3: Verify the Setup
Check Pod status:
```bash
kubectl get pods
```
You should see:
```sql
NAME              READY   STATUS    RESTARTS   AGE
secret-devops     1/1     Running   0          1m
```
Exec into the running container:
```bash
kubectl exec -it secret-devops -- /bin/bash
```
Now, verify the secret was mounted correctly:
```bash
ls /opt/games
cat /opt/games/official.txt
```

You should see the license number or password stored inside /opt/official.txt.

## Concept Recap
| Concept              | Description                                                                              |
| -------------------- | ---------------------------------------------------------------------------------------- |
| **Secret**           | A Kubernetes object that stores sensitive information (like passwords, tokens, or keys). |
| **Pod Volume Mount** | Makes secrets or configurations available inside containers.                             |
| **Base64 Encoding**  | Kubernetes stores secret values in base64 format for safe transport, not encryption.     |

### Best Practices
  - Enable Encryption at Rest for secrets in Kubernetes.
  - Avoid committing secret YAMLs with raw values into version control.
  - Use RBAC policies to restrict access to secrets.
  - Consider using External Secret Managers (like AWS Secrets Manager or HashiCorp Vault) for production setups.

## Summary
| Resource         | Name                      | Purpose                               |
| ---------------- | ------------------------- | ------------------------------------- |
| **Secret**       | `official`                | Stores license/password securely      |
| **Pod**          | `secret-devops`           | Runs a Debian container               |
| **Container**    | `secret-container-devops` | Mounts the secret at `/opt/games`     |
| **Mounted Path** | `/opt/games/official.txt` | Secret file location inside container |

<img width="1552" height="573" alt="Screenshot 2025-10-20 at 10 19 50 PM" src="https://github.com/user-attachments/assets/77496794-b6a6-41d7-ba94-81c200b6cd69" />

