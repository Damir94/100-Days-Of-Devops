# 100 Days of Devops - Day 80: Jenkins Chained Build Setup for Apache Auto-Restart in Stratos Datacenter

## Overview
This project automates the deployment and service management of a web application using Jenkins chained jobs. 
The main goal is to ensure that after every successful deployment, the Apache (httpd) service is automatically restarted on all application servers in the Stratos Datacenter.

The setup uses two Jenkins jobs linked together:

1. nautilus-app-deployment — handles the deployment from the Git repository.
2. manage-services — restarts the Apache service automatically only if the deployment succeeds.

## Environment Setup

Tools and Access Details:
| Component      | URL / Port                                    | Username | Password      |
| -------------- | --------------------------------------------- | -------- | ------------- |
| Jenkins        | Click the **Jenkins** button on the top bar   | admin    | Adm!n321      |
| Gitea          | Port **8090**                                 | sarah    | Sarah_pass123 |
| App Repository | Under user **sarah**, repository name **web** | —        | —             |
The /var/www/html directory on all app servers is shared with the Storage server, ensuring that changes deployed on the Storage server automatically reflect on all app servers.

## Job 1: nautilus-app-deployment
Purpose

This job pulls the latest code from the master branch of the web repository and updates the shared application directory /var/www/html.

## Configuration Steps

1. Create the Job
  - Go to Jenkins Dashboard → New Item.
  - Enter name: nautilus-app-deployment
  - Choose Freestyle Project, then click OK.

2. Configure Git SCM
  - Under Source Code Management → Git:
  - Repository URL: /var/www/html (local git repo tracking origin web)
  - Branches to build: */master

3. Build Triggers
  - Select Poll SCM or configure a webhook in Gitea to trigger builds automatically when developers push code.

4. Build Steps
  - Add Execute Shell step:
```bash
echo "Pulling latest changes..."
cd /var/www/html
git pull origin master
echo "Deployment completed successfully."
```
Ensure Jenkins runs as a user with sudo privileges if permissions are restricted.
5. Post-Build Actions
  - Add a Post-build action → Build other projects.
  - Specify: manage-services
  - Trigger only if build is stable (successful).

## Job 2: manage-services
Purpose

This job is automatically triggered after a successful deployment and restarts the Apache (httpd) service on all app servers.

## Configuration Steps

1. Create the Job
  - Go to Jenkins Dashboard → New Item
  - Enter name: manage-services
  - Choose Freestyle Project, then click OK.

2. Build Triggers
  - Select: Build after other projects are built
  - Upstream Project: nautilus-app-deployment
  - Trigger only if the upstream project is stable.

3. Build Steps
Add Execute Shell step
```bash
echo "Restarting Apache service on all app servers..."
for host in app1 app2 app3
do
  ssh -o StrictHostKeyChecking=no $host "sudo systemctl restart httpd"
  ssh $host "sudo systemctl status httpd | grep Active"
done
echo "Apache service restarted successfully on all app servers."
```
  - Make sure Jenkins has SSH access to all app servers.
  - Validate connectivity using the Jenkins node or SSH credentials plugin if needed.

## Expected Result
  - Successful deployment automatically triggers the downstream job.
  - Apache service restarts on all app servers.
  - Latest content is visible through the Load Balancer URL.
