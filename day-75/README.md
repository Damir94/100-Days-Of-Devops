# 100 Days of Devops - Day 75: Jenkins Slave Node Setup for Nautilus DevOps Project

## Overview
This documentation explains how the Nautilus DevOps team configured a Jenkins master-slave architecture in the Stratos Datacenter (DC).
The purpose of this setup is to allow Jenkins to execute CI/CD jobs and automation tasks on remote application servers (App Servers 1–3) via SSH build agents.

## Objective

The goal was to:
  - Add all application servers as Jenkins slave nodes.
  - Configure SSH-based communication between Jenkins and each app server.
  - Ensure all nodes are online and available for job execution.

## Jenkins Access Details

URL: (Accessible via the Stratos Jenkins button)

Username: admin

Password: Adm!n321

## Step-by-Step Implementation
1. SSH Key Setup on Jenkins Server

Generated an SSH key on the Jenkins master node and distributed it to each app server for passwordless authentication:
```bash
sudo su - jenkins
ssh-keygen -t rsa
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03
```

Verified SSH connectivity:
```bash
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03
```
2. Node Configuration in Jenkins UI

#### App Server 1
  - Node name: App_server_1
  - Label: stapp01
  - Host: stapp01
  - Remote root directory: /home/tony/jenkins
  - Launch method: Launch agent via SSH
  - Credentials: tony (SSH private key from Jenkins master)

#### App Server 2
  - Node name: App_server_2
  - Label: stapp02
  - Host: stapp02
  - Remote root directory: /home/steve/jenkins
  - Credentials: steve

#### App Server 3
  - Node name: App_server_3
  - Label: stapp03
  - Host: stapp03
  - Remote root directory: /home/banner/jenkins
  - Credentials: banner

Each node was configured under:

Manage Jenkins → Nodes and Clouds → New Node

3. Plugin Installation

Installed and configured required plugins:
  - SSH Build Agents Plugin
  - SSH Slaves Plugin

After installation:

Jenkins was restarted via “Restart Jenkins when installation is complete and no jobs are running”.

UI refreshed to verify proper restart.

4. Verification and Validation

After restart, verified that all three nodes came Online successfully:

Manage Jenkins → Nodes → Status: Online

Additional checks:

Confirmed Java was installed on all app servers:
```bash
java -version
```

Verified Jenkins could write to each node’s remote root directory.

### Final Results
| Node Name    | Label   | Hostname | Remote Directory     | Status   |
| ------------ | ------- | -------- | -------------------- | -------- |
| App_server_1 | stapp01 | stapp01  | /home/tony/jenkins   | Online ✅ |
| App_server_2 | stapp02 | stapp02  | /home/steve/jenkins  | Online ✅ |
| App_server_3 | stapp03 | stapp03  | /home/banner/jenkins | Online ✅ |

## Summary
The Jenkins master was successfully configured to connect with all three app servers as SSH build agents. 
This enables distributed CI/CD operations and load-balanced automation execution across multiple servers.

## Key Takeaways
  - SSH-based agents provide a secure and scalable way to extend Jenkins build capabilities.
  - Ensuring passwordless authentication and proper remote root directory permissions is crucial.
  - The SSH Build Agents plugin is required to launch remote agents via SSH.
