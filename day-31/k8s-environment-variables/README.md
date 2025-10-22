# Environment Variable Configuration in Kubernetes

## Overview

In this task, we created a Kubernetes Pod named envars that demonstrates how to define and 
se environment variables dynamically derived from Kubernetes Pod and Node metadata.
This setup helps developers and DevOps engineers understand how to inject runtime information 
(like Pod name, Node name, IP, and Service Account) into containers for configuration, logging, or monitoring purposes.

## Objective

To configure a Pod that:
  - Uses environment variables derived from its own runtime fields using the fieldRef feature.
  - Periodically prints those values to help visualize dynamic metadata at runtime.
  - Runs a simple Apache HTTPD container (httpd:latest) to demonstrate these capabilities.

## Configuration Details
Pod Name:
 - envars
Container Name:
 - fieldref-container
Image:
 - httpd:latest

Command and Arguments:
 - The container runs a continuous shell loop that prints out key environment variables every 10 seconds.
```bash
while true; do
  echo -en '\n';
  printenv NODE_NAME POD_NAME;
  printenv POD_IP POD_SERVICE_ACCOUNT;
  sleep 10;
done;
```
## Environment Variables
The Pod defines four environment variables using the valueFrom.fieldRef property, which allows automatic population of values from the Pod’s metadata and status.

| Environment Variable  | Field Path                | Description                                            |
| --------------------- | ------------------------- | ------------------------------------------------------ |
| `NODE_NAME`           | `spec.nodeName`           | The name of the Kubernetes node the Pod is running on. |
| `POD_NAME`            | `metadata.name`           | The name of the Pod.                                   |
| `POD_IP`              | `status.podIP`            | The IP address assigned to the Pod.                    |
| `POD_SERVICE_ACCOUNT` | `spec.serviceAccountName` | The Service Account associated with the Pod.           |


## YAML Definition
Here’s the final configuration file used to deploy the Pod:
```bash
apiVersion: v1
kind: Pod
metadata:
  name: envars
spec:
  containers:
  - name: fieldref-container
    image: httpd:latest
    command: ["sh", "-c"]
    args:
      - |
        while true; do
          echo -en '\n';
          printenv NODE_NAME POD_NAME;
          printenv POD_IP POD_SERVICE_ACCOUNT;
          sleep 10;
        done;
    env:
      - name: NODE_NAME
        valueFrom:
          fieldRef:
            fieldPath: spec.nodeName
      - name: POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
      - name: POD_SERVICE_ACCOUNT
        valueFrom:
          fieldRef:
            fieldPath: spec.serviceAccountName
  restartPolicy: Never
```
## Deployment Steps
1. Apply the configuration:
```bash
kubectl apply -f envars.yaml
```
2. Check Pod status:
```bash
kubectl get pods
```
3. View logs to see environment variables in action:
```bash
kubectl logs envars
```
4. Exec into the Pod:
```bash
kubectl exec -it envars -- sh
```
Then run:
```bash
printenv NODE_NAME POD_NAME POD_IP POD_SERVICE_ACCOUNT
```
<img width="1343" height="562" alt="Screenshot 2025-10-22 at 8 31 09 AM" src="https://github.com/user-attachments/assets/7f22edde-088d-4f41-95c2-24b4dc16b350" />

## Key Takeaways

 - Kubernetes allows you to dynamically inject Pod and Node metadata into containers through field references.
 - This approach is declarative, meaning no scripting or manual configuration is required inside the container.
 - Such configurations are commonly used for observability, logging, and cluster-aware application setup.

## Example Use Cases
 - Automatically tagging logs with Pod name and node location.
 - Setting up configurations for distributed tracing systems.
 - Debugging environment-specific deployments.
 - Validating Pod metadata for automation scripts or init containers.
## Conclusion
This task demonstrates the power of Kubernetes fieldRef environment variables in making applications metadata-aware. 
By leveraging these dynamic values, you can simplify configuration management and gain more visibility into runtime environments without hardcoding any details.
