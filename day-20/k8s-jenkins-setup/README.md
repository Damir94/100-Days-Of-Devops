# Jenkins CI Server Deployment on Kubernetes

## Overview
The Nautilus DevOps team is setting up a Jenkins CI server on a Kubernetes cluster to automate build and deployment pipelines for multiple projects.
This guide documents the steps, configuration files, and commands used to successfully deploy Jenkins in a Kubernetes environment.

## Objectives
The goal of this setup is to:
 - Create a dedicated namespace for Jenkins.
 - Deploy a Jenkins server using the official jenkins/jenkins image.
 - Expose the Jenkins UI via a NodePort service to access it externally.

## Kubernetes Components
1. Create Namespace
A namespace called jenkins isolates Jenkins resources from other workloads in the cluster.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: jenkins
```
Apply using:
```bash
kubectl apply -f namespace.yaml
```

2. Jenkins Deployment
The deployment ensures that the Jenkins server runs in a managed, self-healing state within Kubernetes.
Deployment Details:
 - Name: jenkins-deployment
 - Namespace: jenkins
 - Label: app: jenkins
 - Container Name: jenkins-container
 - Image: jenkins/jenkins
 - Container Port: 8080
 - Replicas: 1

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-deployment
  namespace: jenkins
  labels:
    app: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
        - name: jenkins-container
          image: jenkins/jenkins
          ports:
            - containerPort: 8080

```
Apply using:
```bash
kubectl apply -f jenkins-deployment.yaml
```

3. Jenkins Service
The service exposes Jenkins externally through a NodePort so users can access the web UI.
Service Details:
 - Name: jenkins-service
 - Namespace: jenkins
 - Type: NodePort
 - NodePort: 30008
 - Target Port: 8080
   
```yaml
apiVersion: v1
kind: Service
metadata:
  name: jenkins-service
  namespace: jenkins
spec:
  type: NodePort
  selector:
    app: jenkins
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30008
```
Apply using:
```bash
kubectl apply -f jenkins-service.yaml
```

## Verification
Check that the namespace, deployment, and service are created successfully:
```bash
kubectl get ns
kubectl get pods -n jenkins
kubectl get svc -n jenkins
```
You should see:
 - A namespace named jenkins
 - A pod from jenkins-deployment
 - A service named jenkins-service exposing port 30008

## Accessing Jenkins
To access the Jenkins web UI, open your browser and go to:
```cpp
http://<Node_IP>:30008
```
Replace <Node_IP> with the IP address of any node in your Kubernetes cluster.

<img width="1458" height="1012" alt="Screenshot 2025-10-16 at 6 27 55 AM" src="https://github.com/user-attachments/assets/f03c1c2c-fa57-4295-bb74-6864ffc5a773" />

## Next Steps
Once Jenkins is accessible, you can:
Retrieve the initial admin password from inside the Jenkins pod:
```bash
kubectl exec -it <jenkins-pod-name> -n jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword
```
 - Log in with that password.
 - Install recommended plugins.
 - Start creating CI/CD pipelines for your projects.

## Summary
This setup demonstrates how to deploy Jenkins in a Kubernetes cluster using YAML manifests.
With Jenkins running inside Kubernetes, the Nautilus DevOps team can now automate builds, 
tests, and deployments in a scalable and resilient environment.
