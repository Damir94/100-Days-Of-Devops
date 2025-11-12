# 100 Days of Devops - Day 79: Nautilus App Auto-Deployment Using Jenkins

## Overview
This project automates the deployment of the Nautilus web application whenever a developer pushes new code to the master branch of the Git repository hosted in Gitea.
The CI/CD pipeline is powered by Jenkins and automatically deploys the latest code to the Storage Server, which serves the web app using Apache (httpd) on port 8080.

## Architecture Diagram
```pgsql
+------------+           +-------------+           +----------------+
| Developer  |  Pushes → |   Gitea     |  Webhook →|   Jenkins CI   |
| (Sarah)    |           | Repository  |           | (Build + Deploy)|
+------------+           +-------------+           +----------------+
                                                         |
                                                         |
                                                         v
                                                +----------------+
                                                | Storage Server |
                                                |  Apache (8080) |
                                                |  /var/www/html |
                                                +----------------+
```

## Key Objectives
1. Auto Deployment: Automatically deploy the latest code from Gitea to the Storage Server when any developer pushes to the master branch.
2. Web Server Setup: Use Apache HTTP Server (httpd) to serve the application on port 8080.
3. CI/CD Integration: Configure Jenkins to pull the latest code, deploy it, and restart Apache automatically.
4. Centralized Access: The deployed content should be visible directly at https://<LBR-URL> (no subdirectories).

## Tools and Technologies
| Component           | Purpose                                              |
| ------------------- | ---------------------------------------------------- |
| **Jenkins**         | CI/CD automation tool to build and deploy changes    |
| **Gitea**           | Git-based source code repository for version control |
| **Apache (httpd)**  | Web server to host and serve web application         |
| **Linux (CentOS)**  | Server operating system                              |
| **Shell scripting** | Used for deployment automation inside Jenkins job    |

Step-by-Step Implementation
1. Install and Configure Apache on App Servers

Install Apache and configure it to listen on port 8080:
```bash
sudo yum install httpd -y
sudo sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
sudo systemctl enable httpd
sudo systemctl start httpd
```
2. Jenkins Setup
  - Login to Jenkins:
  - URL: Jenkins button on top bar
  - Username: admin
  - Password: Adm!n321

Install Required Plugins
  - Go to Manage Jenkins → Plugins → Available and install:
  - Git Plugin
  - SSH Agent Plugin
  - Publish Over SSH Plugin
  - Restart Jenkins after installation.

3. Add SSH Credentials
  - Add SSH access for the Storage Server under:
  - Manage Jenkins → Credentials → Global → Add Credentials
      - Kind: SSH Username with password
      - Username: sarah
      - Password: Sarah_pass123
   
4. Create Jenkins Job
Name: nautilus-app-deployment
Type: Freestyle Project

Configure Git Repository
  - Repository URL: http://<gitea-url>/sarah/web.git
  - Branch: */master
  - Add Gitea credentials if required.
    
Configure Build Trigger
Enable:
```vbnet
Build when a change is pushed to Git repository
```

5. Build and Deployment Steps

Add a Build Step → Execute Shell and include:
```bash
# Ensure correct permissions for deployment
sudo chown -R sarah:sarah /var/www/html

# Clean old deployment
sudo rm -rf /var/www/html/*

# Copy new files from Jenkins workspace
sudo cp -r * /var/www/html/

# Restart Apache service
sudo systemctl restart httpd
```
Tip: Make sure sarah user has passwordless sudo access for these commands.
Add this line in /etc/sudoers:
```perl
sarah ALL=(ALL) NOPASSWD: /bin/cp, /bin/rm, /bin/chown, /bin/systemctl
```
6. Test Auto Deployment

Login to Storage Server
```bash
ssh sarah@<storage-server>
cd ~/web
```
Update Web Content
```bash
echo "Welcome to the xFusionCorp Industries" > index.html
git add index.html
git commit -m "Updated homepage content"
git push origin master
```

Observe Jenkins Job

Jenkins should automatically trigger and deploy.

Check Jenkins console logs for confirmation.

Verify Deployment
Open:
```cpp
https://<LBR-URL>
```
You should see:
```css
Welcome to the xFusionCorp Industries
```
## Validation Checklist
| Task                                     | Status |
| ---------------------------------------- | ------ |
| httpd installed and running on port 8080 | ✅      |
| Jenkins job created and named correctly  | ✅      |
| Auto-trigger on `git push` works         | ✅      |
| Code deploys to `/var/www/html`          | ✅      |
| Ownership set to `sarah`                 | ✅      |
| Site accessible on `https://<LBR-URL>`   | ✅      |

## Conclusion
This setup ensures that any code change automatically triggers a Jenkins job, which:
  - Pulls the latest code from the Gitea repository
  - Deploys it to the shared web directory
  - Restarts Apache to reflect new content instantly
The result is a fully automated, continuous deployment workflow for the Nautilus team
