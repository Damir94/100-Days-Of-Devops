# Troubleshooting and Setup of Nginx + PHP-FPM on Kubernetes
This documentation describes the steps taken to troubleshoot and fix the Nginx and PHP-FPM setup in a Kubernetes cluster, 
along with copying the PHP application file into the correct document root.

## Task Overview
 - Investigate why the nginx-phpfpm pod was not serving PHP files.
 - Identify and fix configuration issues between Nginx and PHP-FPM.
 - Copy the index.php file from the jump host into the Nginx container.
 - Verify website functionality through the lab environment.

### Step 1: Check Pod Status
We first checked the status of the pod to understand if containers were running:
```bash
kubectl get pods
```
 - Verified that the pod nginx-phpfpm was running.
 - Confirmed that both Nginx and PHP-FPM containers were healthy.

## Step 2: Identify Container Names
When working with multi-container pods, it’s important to know the exact container names:
```bash
kubectl get pod nginx-phpfpm -o jsonpath='{.spec.containers[*].name}'
```
 - Output showed the container names: nginx and php-fpm.
This step ensures commands like kubectl cp and kubectl exec target the correct container.

## Step 3: Investigate Document Root
Initially, attempts to copy index.php to /usr/share/nginx/html failed with “File not found.”
We then inspected the pod mounts to find the actual Nginx document root:
```bash
kubectl describe pod nginx-phpfpm
```
Mounts revealed:
```css
/etc/nginx/nginx.conf from nginx-config-volume
/var/www/html from shared-files
```
 - Nginx configuration was mounted from a ConfigMap (nginx-config).
 - The correct document root was /var/www/html.

## Step 4: Inspect ConfigMap
The nginx-config ConfigMap contains the Nginx configuration:
```bash
kubectl get configmap nginx-config -o yaml
```
 - Confirmed that root /var/www/html; is set in the server block.
 - Verified the PHP fastcgi configuration was correctly pointing to PHP-FPM.

## Step 5: Copy PHP File to Container
Once the document root was identified, we copied the application file from the jump host:
```bash
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html -c nginx
```
 - -c nginx ensures the file is copied to the correct container.
 - Verified the file exists in the container:
```bash
kubectl exec -it nginx-phpfpm -c nginx -- ls -l /var/www/html
```
Output confirmed:
```diff
index.php
```
## Step 6: Verify Website Functionality
After placing index.php in the correct location:
 - Clicked the Website button in the lab environment.
 - Confirmed the PHP page loaded successfully.
This verified that both Nginx and PHP-FPM were correctly configured, and the application was now functional.

<img width="1432" height="927" alt="Screenshot 2025-10-13 at 8 27 44 PM" src="https://github.com/user-attachments/assets/66c294db-5e69-475f-98b3-9e10d7390b4f" />

## Summary of Troubleshooting Steps
 - Checked pod status and container health.
 - Verified container names for accurate commands.
 - Investigated Nginx mounts and document root paths.
 - Examined ConfigMap for Nginx configuration.
 - Copied the PHP application file to the correct container and path.
 - Verified website functionality.

## Lessons Learned
 - Always check container names in multi-container pods when using kubectl cp or kubectl exec.
 - Verify the document root by inspecting both pod mounts and ConfigMap contents.
 - Ensure Nginx fastcgi configuration correctly points to PHP-FPM within the same pod.
 - Use kubectl describe pod and kubectl logs to identify root causes before making changes.
 - This documentation can be used as a reference for future troubleshooting and deployment tasks with Nginx + PHP-FPM on Kubernetes.
