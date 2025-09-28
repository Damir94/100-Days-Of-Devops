# 100 Days of DevOps - Day 35: Containerizing Applications with Docker on App Server 2

## Overview

The Nautilus DevOps team recently collaborated with the application development team to begin containerizing our applications. As a first step, we focused on setting up a Docker environment on App Server 2 to enable testing and deployment of containerized applications.

This blog documents the installation, configuration, and verification steps completed, serving as a reference for team members and future projects.

## Step 1: System Update

Before installing Docker, we ensured that all system packages were up to date:
```bash
    sudo yum update -y
    sudo yum upgrade -y
```
This ensures compatibility and avoids conflicts during Docker installation.

## Step 2: Installing Docker CE

Docker CE (Community Edition) provides the core Docker engine required for building and running containers.

```bash
    sudo yum install -y apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo yum update -y
    sudo yum install -y docker-ce docker-ce-cli containerd.io
```

## Step 3: Installing Docker Compose
```bash
    DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
    sudo curl -L "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    docker-compose --version
```
This ensures the latest stable version of Docker Compose is installed and executable.

## Step 4: Starting Docker Service
With Docker installed, we initiated the service and enabled it to start on boot:
```bash
    sudo systemctl start docker
    sudo systemctl enable docker
    sudo systemctl status docker
```

## Step 5: Verifying Docker Installation
To confirm that Docker is functioning properly, we ran a test container:
```bash
    sudo docker run hello-world
```
The successful output confirmed that Docker was installed correctly and ready to run containers.

