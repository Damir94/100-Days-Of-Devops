# 100 Days of Devops - Day 82: Static Website Deployment Using Jenkins Pipeline
Automated CI/CD for xFusionCorp Industries Web Application

This project implements a fully automated CI/CD pipeline using Jenkins, Gitea, 
and Apache HTTP Server to deploy a static website across the Nautilus App Servers. The goal is to ensure 
fast, consistent, and reliable deployments using a simple Jenkins pipeline.

## Project Overview

The development team at xFusionCorp Industries is building a new static website hosted in a Gitea repository. 
The DevOps team is responsible for automating deployment so that any updated website content is 
pushed and deployed seamlessly across all app servers.

This automation is achieved using:
  - Gitea – Source code version control
  - Jenkins Pipeline – CI/CD automation
  - Storage Server (NFS) – Shared document root
  - Apache Web Server on App Servers – Serves the website via port 8080
  - Load Balancer – Public entrypoint (http://stlb01:8091)

### Repository Structure
```bash
web/
 └── index.html      # Static site home page
```
The repository is located in Gitea: sarah/web

This repo is cloned on the Storage Server in:
```css
/var/www/html
```
This directory is mounted to all App Servers and serves as their Apache webroot.

Update Workflow
  - Developers modify the website (ex: update index.html)
  - The change is committed and pushed to the master branch
  - Jenkins pipeline is triggered manually (or automatically if configured)
  - Pipeline deploys the latest code to the Storage Server
  - App Servers instantly serve updated content via the shared mount
  - Pipeline tests the live website through the Load Balancer

## Jenkins Pipeline Design
A Jenkins pipeline job named deploy-job is used to deploy and verify the app.
### Stages Overview
| Stage      | Purpose                                                                           |
| ---------- | --------------------------------------------------------------------------------- |
| **Deploy** | Pulls latest code from Gitea into `/var/www/html` on the Storage Server           |
| **Test**   | Validates the website by performing a live HTTP request through the load balancer |

### Pipeline Script
```groovy
pipeline {
    agent any

    stages {

        stage('Deploy') {
            steps {
                echo "Deploying website..."
                sh '''
                    cd /var/www/html
                    git pull origin master
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Testing application..."
                sh '''
                    curl -f http://stlb01:8091
                '''
            }
        }
    }

    post {
        failure {
            echo "Deployment Failed!"
        }
    }
}
```
Testing the Deployment

The pipeline verifies deployment using:
```bash
curl -f http://stlb01:8091
```
  - curl -f forces the stage to fail if:
  - website is unreachable
  - HTTP response is not code 200
If the site loads successfully, the pipeline passes.

### Accessing the Website
After deployment, the website becomes available at:
```bash
http://stlb01:8091
```
You should see:
```css
Welcome to xFusionCorp Industries
```
The URL must load the content directly at the root — not under /web or any subdirectory.

### Prerequisites
On Storage Server
  - Git installed
  - Repo cloned under /var/www/html

On Jenkins Server
  - Required plugins:
  - Git Plugin
  - Pipeline
  - Pipeline: GitHub / SCM

SSH access to Storage Server or direct local path

Jenkins user with required permissions

On App Servers
  - Apache installed and running on port 8080
  - /var/www/html mounted from Storage Server

### Key Benefits of This Setup
  - Fully automated deployments
  - Zero-downtime updates (NFS shared mount)
  - Centralized build and deployment process
  - Fast rollback (via Git versioning)
  - Built-in health check through the Test stage

### Conclusion
This CI/CD pipeline provides a simple, reliable, and scalable way to deploy static websites across multiple app servers. 
Using Jenkins automation combined with Gitea version control and a shared storage architecture ensures quick deployments and consistent website delivery.
