# Tomcat Application Deployment on Kubernetes
## Overview
The Nautilus DevOps team deployed a new Java-based Tomcat application on an existing Kubernetes cluster. 
This document describes the steps and configurations used to set up the environment, 
deploy the Tomcat application, and expose it to users for external access.

## Objectives
 - Create a dedicated namespace for the Tomcat application.
 - Deploy a Tomcat-based container using a custom image.
 - Expose the application through a NodePort Service for external access.
 - Ensure the application is running and reachable on the specified port.

### Step 1: Namespace Setup
A new namespace was created to logically separate and manage the Tomcat resources.
```bash
apiVersion: v1
kind: Namespace
metadata:
  name: tomcat-namespace-datacenter
```
Namespace helps in resource isolation, organization, and efficient access control within the cluster.

### Step 2: Tomcat Deployment
A Kubernetes Deployment named tomcat-deployment-datacenter was created under the namespace tomcat-namespace-datacenter.
This deployment ensures that the Tomcat container runs continuously, and Kubernetes automatically manages the desired number of replicas.

#### Deployment Specification:
 - Name: tomcat-deployment-datacenter
 - Namespace: tomcat-namespace-datacenter
 - Replicas: 1
 - Container Name: tomcat-container-datacenter
 - Image: gcr.io/kodekloud/centos-ssh-enabled:tomcat
 - Container Port: 8080

YAML Definition:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment-datacenter
  namespace: tomcat-namespace-datacenter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tomcat-app
  template:
    metadata:
      labels:
        app: tomcat-app
    spec:
      containers:
        - name: tomcat-container-datacenter
          image: gcr.io/kodekloud/centos-ssh-enabled:tomcat
          ports:
            - containerPort: 8080
```

### Step 3: Service Configuration
A NodePort Service was created to make the Tomcat application accessible from outside the cluster.
This service routes external traffic from the node port to the internal pod port.

#### Service Specification:
 - Name: tomcat-service-datacenter
 - Namespace: tomcat-namespace-datacenter
 - Type: NodePort
 - Port: 8080
 - Target Port: 8080
 - NodePort: 32227

YAML Definition:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: tomcat-service-datacenter
  namespace: tomcat-namespace-datacenter
spec:
  type: NodePort
  selector:
    app: tomcat-app
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 32227
```

### Step 4: Validation
Once all manifests were applied, the team verified that:
 - The pod was in a Running state.
 - The service correctly mapped traffic to the pod.
 - The application was accessible using the Node IP and port.

Verification Commands:
```bash
kubectl get pods -n tomcat-namespace-datacenter
kubectl get svc -n tomcat-namespace-datacenter
```

Access URL:
```bash
http://<node-ip>:32227
```
<img width="574" height="161" alt="Screenshot 2025-10-16 at 9 34 33 PM" src="https://github.com/user-attachments/assets/a8667848-391b-4391-9adb-1b7b4a28ac85" />


## Summary
| Resource Type | Name                           | Namespace                     | Purpose                           |
| ------------- | ------------------------------ | ----------------------------- | --------------------------------- |
| Namespace     | `tomcat-namespace-datacenter`  | -                             | Isolates Tomcat resources         |
| Deployment    | `tomcat-deployment-datacenter` | `tomcat-namespace-datacenter` | Manages Tomcat pods               |
| Service       | `tomcat-service-datacenter`    | `tomcat-namespace-datacenter` | Exposes the Tomcat app externally |

## Outcome
The Tomcat application was successfully deployed and made accessible externally through NodePort 32227.
This setup ensures a stable, manageable, and scalable environment for future Java-based application deployments within the cluster.
