# Configure and Test Environment Variables in a Kubernetes Pod

##Overview
The Nautilus DevOps team is setting up pre-requisites for an application that sends greetings to different users.
As part of the testing phase, a sample Kubernetes Pod needs to be created to print a greeting message using environment variables.
This guide explains how to create, configure, and test a Pod that prints a custom message using environment variables.

### Task Requirements
 - Pod Name: print-envars-greeting
 - Container Name: print-env-container
 - Image: bash
 - Restart Policy: Never

### Environment Variables
| Variable   | Value        |
| ---------- | ------------ |
| `GREETING` | `Welcome to` |
| `COMPANY`  | `Stratos`    |
| `GROUP`    | `Datacenter` |

### Command to Run
The container should execute the following command when it starts:
```bash
/bin/sh -c 'echo "$(GREETING) $(COMPANY) $(GROUP)"'
```
This command will combine the environment variable values and print the greeting message to the console.

### Pod Manifest File
Create a YAML file named print-envars-greeting.yaml with the following configuration:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting
spec:
  containers:
    - name: print-env-container
      image: bash
      command: ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
      env:
        - name: GREETING
          value: "Welcome to"
        - name: COMPANY
          value: "Stratos"
        - name: GROUP
          value: "Datacenter"
  restartPolicy: Never
```

## Steps to Deploy
1. Apply the Pod configuration
Run the following command to create the Pod in your Kubernetes cluster
```bash
kubectl apply -f print-envars-greeting.yaml
```

2. Verify the Pod Status
Check whether the Pod has been created successfully and completed execution:
```bash
kubectl get pods
```
You should see:
```arduino
NAME                    READY   STATUS      RESTARTS   AGE
print-envars-greeting   0/1     Completed   0          5s
```

3. View the Logs (Output)
To see the output of the command executed inside the Pod, run:
```bash
kubectl logs -f print-envars-greeting
```
Expected Output:
```css
Welcome to Stratos Datacenter
```
<img width="1448" height="534" alt="Screenshot 2025-10-15 at 9 40 19 AM" src="https://github.com/user-attachments/assets/2d353362-0cc7-4328-81c6-88c79c00ab16" />

## Key Learnings
 - You learned how to set environment variables inside a Pod.
 - You used the command field to run a custom shell command.
 - You set the Pod’s restart policy to Never to prevent it from restarting after completion.
 - You verified the Pod’s behavior using kubectl logs.

## Conclusion
This task demonstrates how to pass and print environment variables inside a Kubernetes Pod.
It’s a useful foundational step for configuring Pods that depend on dynamic environment-based settings (e.g., app configuration, credentials, or greetings logic).

Final Output:

Welcome to Stratos Datacenter
