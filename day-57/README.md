# 100 Days of DevOps - Day 57: Print Environment Variables Greeting Pod

## Overview
The Nautilus DevOps team is setting up a test environment to verify how environment variables can be injected into containers in Kubernetes.
This exercise helps demonstrate how applications can use environment variables to pass configuration or data dynamically during runtime.
In this task, we created a simple Pod that prints a greeting message by combining values from multiple environment variables.

### Design Concept
### Purpose
This setup is part of the prerequisites for an upcoming greetings application.
The idea is to simulate how an application can use environment variables to build dynamic messages.

#### How It Works
The Pod runs a single container that:
  - Uses a lightweight bash image.
  - Reads three environment variables (GREETING, COMPANY, and GROUP).
  - Combines and prints them as a single greeting message using an inline shell command.
This demonstrates a key concept in Kubernetes: injecting and using environment variables inside containers.

### Implementation Details
1. Pod Configuration
 - Pod Name: print-envars-greeting
 - Container Name: print-env-container
 - Image: bash
 - Restart Policy: Never — ensures the Pod doesn’t restart after execution.
 - Command:
```bash
/bin/sh -c 'echo "$(GREETING) $(COMPANY) $(GROUP)"'
```
This prints a concatenated string using the values of the defined environment variables.

2. Environment Variables
   
| Variable | Value      | Purpose                      |
| -------- | ---------- | ---------------------------- |
| GREETING | Welcome to | Opening text of the greeting |
| COMPANY  | Nautilus   | Company name                 |
| GROUP    | Ltd        | Suffix for the company       |

### Pod Manifest
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting
spec:
  restartPolicy: Never
  containers:
    - name: print-env-container
      image: bash
      command: ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
      env:
        - name: GREETING
          value: "Welcome to"
        - name: COMPANY
          value: "Nautilus"
        - name: GROUP
          value: "Ltd"
```

### Deployment Steps
1. Apply the Pod manifest:
```bash
kubectl apply -f print-envars-greeting.yaml
```
2. Verify the Pod status:
```bash
kubectl get pods
```
3. View the output:
```bash
kubectl logs -f print-envars-greeting
```
4. Expected Output:
```css
Welcome to Nautilus Ltd
```



## Key Takeaways
- Environment variables are a powerful way to configure containers without hardcoding values.
- RestartPolicy: Never prevents repeated execution of short-lived pods.
- This pattern is commonly used in:
    - Configuring applications dynamically.
    - Setting up CI/CD pipelines.
    - Managing secrets or runtime configurations.

## Future Enhancements
  - Inject environment variables from ConfigMaps or Secrets instead of hardcoding them.
  - Combine with a Deployment or Job for automated batch tasks.
  - Extend this concept for a real application that sends personalized greetings to users.

## Conclusion
This exercise highlights a simple but essential DevOps concept: using environment variables in Kubernetes to pass dynamic configuration to containers.
The print-envars-greeting Pod successfully demonstrates how to configure, run, and verify environment-based output in a controlled Kubernetes environment.
