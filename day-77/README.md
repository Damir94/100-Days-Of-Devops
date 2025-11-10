# 100 Days of Devops - Day 77: Jenkins Pipeline Deployment for xFusionCorp Static Web App

## Project Overview
The xFusionCorp Industries DevOps team was tasked to automate the deployment of a new static web application on Nautilus App Servers using Jenkins Pipeline.
The application code is maintained in a Gitea repository and should be automatically deployed to the Storage Server, which serves as the shared document root for all App Servers via /var/www/html.

## Task Requirements

1. Access Jenkins UI
    - URL: (Click Jenkins button in top bar)
    - Username: admin
    - Password: Adm!n321
      
2. Access Gitea UI
    - URL: (Click Gitea button in top bar)
    - Username: sarah
    - Password: Sarah_pass123
    - Repository: web_app (already cloned under /var/www/html on Storage Server)

3. Add Jenkins Slave Node
    - Node name: Storage Server
    - Label: ststor01
    - Remote root directory: /var/www/html
    - Launch method: SSH to agent

4. Pipeline Job
    - Job name: xfusion-webapp-job
    - Type: Pipeline (not Multibranch)
    - Stage name: Deploy (case-sensitive)

5. Deployment Target
     - Deploy web_app repository contents into /var/www/html on the Storage Server.
     - Apache on App Servers runs on port 8080.
     - The Load Balancer URL should serve content directly at the root (e.g., https://<LBR-URL>).

## Jenkins Pipeline Script
```groovy
pipeline {
    agent { label 'ststor01' }

    stages {
        stage('Deploy') {
            steps {
                echo "Deploying web_app code to /var/www/html"

                // Clean existing files
                sh 'rm -rf /var/www/html/*'

                // Clone the latest web_app code from Gitea
                sh 'git clone http://sarah@<GITEA-IP>:3000/sarah/web_app.git /var/www/html'

                // Flatten directory structure if needed
                sh 'if [ -d /var/www/html/web_app ]; then mv /var/www/html/web_app/* /var/www/html/; rm -rf /var/www/html/web_app; fi'

                echo "Deployment completed successfully!"
            }
        }
    }
}
```
Replace <GITEA-IP> with your actual Gitea server IP address.

## Deployment Steps
  - Login to Jenkins and navigate to xfusion-webapp-job.
  - Click Build Now to trigger the deployment.
  - Monitor progress via the Console Output — ensure the clone and move steps complete successfully.
  - Verify deployment on the App URL (Load Balancer):
  - https://<LBR-URL>
The main page should load directly — no /web_app subdirectory in the URL.

## Verification Checklist
| Requirement                                           | Status |
| ----------------------------------------------------- | ------ |
| Jenkins slave node `Storage Server` added             | ✅      |
| Node label `ststor01` configured                      | ✅      |
| Pipeline job `xfusion-webapp-job` created             | ✅      |
| Stage name `Deploy` created                           | ✅      |
| Gitea repository deployed to `/var/www/html`          | ✅      |
| Web application accessible via Load Balancer root URL | ✅      |


## Conclusion
This Jenkins pipeline automates deployment for the static web_app repository, ensuring the latest code from Gitea is consistently deployed to all App Servers through the shared /var/www/html mount point on the Storage Server.
The setup meets the DevOps team’s goal of enabling continuous integration and simplified release management.

