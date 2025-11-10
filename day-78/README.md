# 100 Days of Devops - Day 78: Jenkins Pipeline Deployment – xFusionCorp Industries

## Overview
The xFusionCorp Industries DevOps team has set up an automated deployment pipeline using Jenkins to deploy a static website onto the Nautilus App Servers.
This documentation explains the complete setup, configuration, and execution of the Jenkins pipeline job named xfusion-webapp-job.

## Project Background

The development team is building a static website stored in a Gitea repository called web_app.
Our goal is to:
  - Deploy this code to App Servers automatically via Jenkins.
  - Allow developers to choose which branch (master or feature) to deploy.
  - Serve the website directly through the Load Balancer (LB) URL without subdirectories.

## Environment Details
| Component                    | Description                               |
| ---------------------------- | ----------------------------------------- |
| **Version Control**          | Gitea (`web_app` repo under user `sarah`) |
| **CI/CD Tool**               | Jenkins                                   |
| **Slave Node (Agent)**       | Storage Server                            |
| **Label**                    | `ststor01`                                |
| **Remote Root Directory**    | `/var/www/html`                           |
| **Web Server**               | Apache (running on port 8080)             |
| **Repository Path (Cloned)** | `/var/www/html/web_app`                   |
| **Pipeline Job Name**        | `xfusion-webapp-job`                      |

## Access Credentials
| Tool    | Username | Password        |
| ------- | -------- | --------------- |
| Jenkins | `admin`  | `Adm!n321`      |
| Gitea   | `sarah`  | `Sarah_pass123` |

### Jenkins Node Configuration

1. Navigate to: Manage Jenkins → Nodes → New Node
2. Add a new node named Storage Server
  - Type: Permanent agen
  - Label: ststor01
  - Remote Root Directory: /var/www/html
  - Usage: Use this node as much as possible

3. Configure the node connection (SSH or Jenkins agent) and save.

### Jenkins Pipeline Job Setup
Step 1: Create the Pipeline Job
  - Go to Jenkins Dashboard → New Item
  - Enter job name: xfusion-webapp-job
  - Select Pipeline ( Do not use Multibranch Pipeline)
  - Check This project is parameterized
      - Add a String Parameter:
      - Name: BRANCH
      - Default Value: master
      - Description: Enter the branch name to deploy (master or feature).

Step 2: Pipeline Script
Paste the following script in the Pipeline Script section:
```groovy
pipeline {
    agent { label 'ststor01' }

    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to deploy (master or feature)')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    if (params.BRANCH == 'master' || params.BRANCH == 'feature') {
                        echo "Deploying ${params.BRANCH} branch..."
                        dir('/var/www/html') {
                            // Clean old files
                            sh 'rm -rf *'
                            // Clone correct branch from Gitea
                            sh "git clone -b ${params.BRANCH} http://sarah:Sarah_pass123@gitea_url/sarah/web_app.git ."
                        }
                        echo "Deployment of ${params.BRANCH} branch completed."
                    } else {
                        error "Invalid branch name! Please specify 'master' or 'feature'."
                    }
                }
            }
        }
    }
}
```
Replace gitea_url with your actual Gitea base URL (visible in your Gitea interface).

### Deployment Process

1. From Jenkins dashboard, open xfusion-webapp-job.
2. Click Build with Parameters.
3. Enter one of the following in the BRANCH parameter:
  - master → Deploys production branch.
  - feature → Deploys feature branch.
4. Wait for the build to complete successfully.

### Expected Outcome
  - The correct branch is deployed to /var/www/html.
  - The Apache web server serves updated static content.
  - The website is accessible via the Load Balancer main URL.
  - Developers can easily switch deployment between master and feature branches using Jenkins parameters.

### Summary
This setup demonstrates a simple yet powerful CI/CD pipeline for static web applications:
  - Parameterized deployment for flexible branch management.
  - Agent-based execution on a dedicated storage node.
  - Automated cloning and deployment from a Git repository.
  - Seamless integration with Apache servers behind a load balancer.
