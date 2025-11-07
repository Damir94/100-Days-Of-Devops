# Jenkins Installation & Configuration Guide

## Overview
The DevOps team at xFusionCorp Industries has begun setting up a Continuous Integration and Continuous Deployment (CI/CD) environment using Jenkins as the automation server.

This document provides a step-by-step guide on installing Jenkins via YUM, configuring it, and creating an admin user for initial setup.

### Server Access
  - Server: jenkins
  - Access Method: Connect from the jump host
  - Login as: root
  - Password: S3curePass
```bash
ssh root@jenkins
# Password: S3curePass
```
Step 1: Install Java (Prerequisite)
Jenkins requires Java to run. The latest stable and supported version is Java 17.
```bash
yum install java-17-openjdk -y
```
Verify installation:
```bash
java -version
```
Step 2: Add Jenkins Repository
Add the official Jenkins repository and import the GPG key to ensure authenticity.
```bash
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```
Step 3: Install Jenkins Using YUM
```bash
yum install jenkins -y
```
Step 4: Start and Enable Jenkins Service
```bash
systemctl start jenkins
systemctl enable jenkins
```
Troubleshooting Timeout (if any)

If you encounter a timeout error when starting Jenkins:
```bash
systemctl edit jenkins
```
Add:
```ini
[Service]
TimeoutStartSec=300
```
Then reload and restart:
```bash
systemctl daemon-reexec
systemctl restart jenkins
```
Step 5: Access Jenkins Web UI
Once started, Jenkins runs on port 8080.
Access Jenkins using your browser:
```bash
http://<jenkins-server-ip>:8080
```
Step 6: Unlock Jenkins
Retrieve the initial admin password:
```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```
Copy and paste it into the Jenkins setup wizard.

Step 7: Create the Admin User
Create the Jenkins administrator account with the following details:
| Field         | Value                                    |
| ------------- | ---------------------------------------- |
| **Username**  | `theadmin`                               |
| **Password**  | `Adm!n321`                               |
| **Full Name** | `Yousuf`                                 |
| **Email**     | `yousuf@jenkins.stratos.xfusioncorp.com` |


Step 8: Install Plugins
Choose the “Install suggested plugins” option during setup.
Wait until all recommended plugins are installed automatically.

Step 9: Verify Installation
Confirm that Jenkins is active and running:
```bash
systemctl status jenkins
```
Check that it’s listening on port 8080:
```bash
ss -tuln | grep 8080
```
You can also verify with:
```bash
curl -I http://localhost:8080
```
Step 10: Login and Confirm Setup
Once Jenkins installation and configuration are complete:
```bash
Visit http://<jenkins-server-ip>:8080
```
## Summary
  - You’ve successfully:
  - Installed Java 17 and Jenkins using YUM.
  - Started and enabled Jenkins as a service.
  - Created the admin user theadmin with specified credentials.
  - Installed default plugins and verified Jenkins is running.
