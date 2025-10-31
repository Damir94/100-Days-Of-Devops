# Git Repository Setup for Nautilus Development Project

## Overview
The Nautilus DevOps team has set up a central Git repository on the Storage Server to support the new application development project. 
This repository serves as a shared location for developers to push and manage application code collaboratively.

## Steps Performed
1. Git Installation
   
Git was installed on the Storage Server using the yum package manager to ensure version control capabilities are available.
```bash
sudo yum install git -y
```
2. Repository Creation

A bare Git repository was created under the /opt directory.
A bare repository is ideal for central storage since it contains no working directory and is meant for collaboration via remote pushes and pulls.
```bash
sudo git init --bare /opt/apps.git
```
3. Verification
To verify the repository setup:
```bash
ls -ld /opt/apps.git
```
Expected output includes standard Git directories such as:
```arduino
branches/  config  description  HEAD  hooks/  info/  objects/  refs/
```
4. Developer Usage
Developers can now clone and start using the repository with:
```bash
git clone ssh://<user>@<storage-server-ip>/opt/apps.git
```
After cloning, they can commit and push code changes as usual.
## Repository Details
| Item                | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| **Repository Name** | apps.git                                                     |
| **Location**        | /opt/apps.git                                                |
| **Type**            | Bare Repository                                              |
| **Purpose**         | Central code repository for the Nautilus application project |
