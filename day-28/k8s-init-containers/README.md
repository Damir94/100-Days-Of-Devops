# Using Init Containers in Kubernetes — xFusionCorp Example

## Overview
In this project, we tested the use of init containers in Kubernetes to perform pre-deployment configuration tasks before the main application container starts.
Some application setups require configuration or initialization steps that cannot be done inside the container image itself — for example, preparing files or directories.
To solve this, the DevOps team at xFusionCorp Industries implemented init containers that execute these one-time setup commands automatically at deployment time.

## Objective
We deployed an example application called ic-deploy-xfusion on our Kubernetes cluster.
The goal was to:
 - Use an init container to create a custom message file before the main container starts.
 - Share data between the init and main containers using a shared volume (emptyDir).
 - Keep the main container running and continuously displaying the initialization message.

## Deployment Details
Deployment Name: ic-deploy-xfusion
Replicas: 1
App Label: ic-xfusion

## Init Container
 - Name: ic-msg-xfusion
 - Image: debian:latest
 - Purpose: Creates a message file before the main container starts.
 - Command Executed:
```bash
    /bin/bash -c "echo Init Done - Welcome to xFusionCorp Industries > /ic/ecommerce"
```
 - Volume Mount:
    - Mount Path: /ic
    - Volume Name: ic-volume-xfusion

## Main Container
 - Name: ic-main-xfusion
 - Image: debian:latest
 - Purpose: Reads and displays the message written by the init container every 5 seconds.
 - Command Executed:
```bash
    /bin/bash -c "while true; do cat /ic/ecommerce; sleep 5; done"
```
 - Volume Mount:
    - Mount Path: /ic
    - Volume Name: ic-volume-xfusion

## Shared Volume
- Name: ic-volume-xfusion
- Type: emptyDir
This temporary directory is created when a Pod starts and deleted when the Pod stops. It is shared between all containers in the Pod.

## Complete YAML Manifest
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-xfusion
  template:
    metadata:
      labels:
        app: ic-xfusion
    spec:
      initContainers:
      - name: ic-msg-xfusion
        image: debian:latest
        command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/ecommerce"]
        volumeMounts:
        - name: ic-volume-xfusion
          mountPath: /ic

      containers:
      - name: ic-main-xfusion
        image: debian:latest
        command: ["/bin/bash", "-c", "while true; do cat /ic/ecommerce; sleep 5; done"]
        volumeMounts:
        - name: ic-volume-xfusion
          mountPath: /ic

      volumes:
      - name: ic-volume-xfusion
        emptyDir: {}
```
## Explanation
  - Deployment name: ic-deploy-xfusion
  - Label: app: ic-xfusion (used for selector and template)
  - Init container (ic-msg-xfusion):
      - Uses debian:latest
      - Runs a command that writes the text "Init Done - Welcome to xFusionCorp Industries" into /ic/ecommerce
      - Mounts the volume ic-volume-xfusion at /ic
 - Main container (ic-main-xfusion):
   - Uses the same debian:latest image
   - Continuously reads and prints the contents of /ic/ecommerce every 5 seconds
   - Shares the same volume as the init container, ensuring it can read the initialized file
 - Volume: emptyDir (shared between init and main containers)

## How It Works
1. When the Pod starts, Kubernetes runs the init container first.
It writes a welcome message into /ic/ecommerce inside the shared volume.
2. Once the init container completes successfully, the main container starts.
3. The main container continuously reads and prints the message file’s content every 5 seconds.
4. If the Pod restarts, the init container will run again before the main container starts.

## Verification
To verify the setup:
```bash
kubectl get pods
kubectl logs <pod-name> -c ic-main-xfusion
```
You should see output similar to:
```css
Init Done - Welcome to xFusionCorp Industries
Init Done - Welcome to xFusionCorp Industries
...
```
## Key Takeaways
 - Init containers are a great way to handle pre-deployment configuration steps in Kubernetes.
 - Using emptyDir volumes allows containers in the same Pod to share files easily.
 - This pattern ensures that your application starts only after all pre-requisite configurations are complete.
