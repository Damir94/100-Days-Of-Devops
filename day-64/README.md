# Python Flask App Deployment on Kubernetes
## Overview
This document explains the deployment, debugging, and troubleshooting process for a Python Flask application running on a Kubernetes cluster. 
The app was deployed using a Deployment and Service configuration and made accessible through a NodePort.

## Deployment Details
  - Deployment Name: python-deployment-xfusion
  - Container Image: poroko/flask-demo-appimage:latest
  - Replicas: 1
  - Container Port (Flask default): 5000
  - Service Type: NodePort
  - NodePort: 32345
  - TargetPort: 5000

## Issue Description
During initial deployment, the application pods failed to start. Running:
```bash
kubectl describe pod <pod-name>
```
showed the following error:
```pgsql
Failed to pull image "poroko/flask-app-demo": rpc error: code = Unknown desc = 
failed to pull and unpack image "docker.io/poroko/flask-app-demo:latest": 
pull access denied, repository does not exist or may require authorization
```
Root Causes
1. Incorrect image name:
The Deployment referenced poroko/flask-app-demo, which does not exist in Docker Hub.

2. Incorrect targetPort:
The Service was pointing to the wrong targetPort, preventing external access to the container.

## Troubleshooting Steps
1. Checked Pod and Event Logs
```bash
kubectl get pods
kubectl describe pod <pod-name>
```
Identified ErrImagePull and ImagePullBackOff errors.

2. Verified Image Availability
Tried pulling the image manually:
```bash
docker pull poroko/flask-app-demo
```
Confirmed the image was not found.
Correct image name found: poroko/flask-demo-appimage:latest.

3. Updated Deployment Configuration
Edited deployment to use the correct image and port:
```yaml
containers:
- name: python-container
  image: poroko/flask-demo-app:latest
  ports:
  - containerPort: 5000
```
Applied the changes:
```bash
kubectl apply -f deployment.yaml
```
4. Fixed Service Configuration
Ensured the Service exposes the correct ports:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: python-deployment-xfusion
spec:
  type: NodePort
  selector:
    app: python-deployment-xfusion
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
      nodePort: 32345
```
Applied:
```bash
kubectl apply -f service.yaml
```
5. Verified Deployment
```bash
kubectl get pods -o wide
kubectl get svc
```
Output:
```sql
NAME                         READY   STATUS    RESTARTS   AGE
python-deployment-xfusion-... 1/1     Running   0          2m

NAME                        TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
python-deployment-xfusion   NodePort   10.96.120.10   <none>        5000:32345/TCP   2m
```
## Accessing the Application
Find the node IP:
```bash
kubectl get nodes -o wide
```
Then access the app in your browser:

<img width="598" height="216" alt="Screenshot 2025-10-24 at 7 01 09 AM" src="https://github.com/user-attachments/assets/596d398a-071e-4d8f-ae24-2794a15948a5" />

```cpp
http://<NodeIP>:32345
```
If using Minikube:
```bash
minikube service python-deployment-xfusion --url
```

## Final Outcome

| Component  | Status       | Notes                                     |
| ---------- | ------------ | ----------------------------------------- |
| Deployment | ✅ Running    | Using `poroko/flask-demo-appimage:latest` |
| Service    | ✅ Accessible | NodePort `32345`, targetPort `5000`       |
| Pods       | ✅ Healthy    | No image pull or config errors            |


## Key Learnings
  - Always verify image names and tags before deploying.
  - Ensure Service targetPort matches the container’s internal port.
  - Use kubectl describe and event logs to quickly locate image and port-related issues.
  - Keep YAML manifests separated for better readability and reusability.

