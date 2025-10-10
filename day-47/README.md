# 100 Days 0f Devops - Day 47: Dockerizing and Deploying a Python Application

## Overview
The Nautilus DevOps team needed to containerize a Python application and deploy it on App Server 1. 
This process involved creating a Dockerfile, building a Docker image, and running a containerized instance of the app. 
The Python app dependencies were already provided in a requirements.txt file located under /python_app/src/.

### Step 1: Preparing the Environment
We started by navigating to the application directory on App Server 1:
```bash
cd /python_app
```
Inside this directory, the folder structure looked like this:
```bash
/python_app
└── src/
    ├── requirements.txt
    ├── server.py
    └── (other app files)
```
### Step 2: Creating the Dockerfile
A Dockerfile was created under /python_app to define how the container image should be built.
```Dockerfile
# Use an official Python base image
FROM python:3.9

# Set working directory inside the container
WORKDIR /app

# Copy requirements.txt to the container
COPY src/requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy all app files into the container
COPY src/ .

# Expose the application port
EXPOSE 8088

# Command to run the Python app
CMD ["python", "server.py"]
```

### Explanation:
 - Base Image: We used python:3.9 as it provides a ready-to-use Python environment.
 - Dependencies: The requirements.txt file ensures all necessary libraries are installed.
 - Port Exposure: Port 8088 is exposed for external access.
 - Command: The container runs server.py when started.

### Step 3: Building the Docker Image
After creating the Dockerfile, we built the Docker image:
```bash
docker build -t nautilus/python-app .
```
This command created a new image named nautilus/python-app, which contains our Python application and all its dependencies.

### Step 4: Running the Container
We launched a container from the built image using:
```bash
docker run -d --name pythonapp_nautilus -p 8097:8088 nautilus/python-app
```
### Key points:
 - -d runs the container in detached mode.
 - The container name is pythonapp_nautilus.
 - Port 8088 (inside container) is mapped to port 8097 (on host).

### Step 5: Verifying the Deployment
To ensure the container is running:
```bash
docker ps
```

Expected output:
```bash
CONTAINER ID   IMAGE                 COMMAND             STATUS         PORTS
xxxxxx         nautilus/python-app   "python server.py"  Up ...         0.0.0.0:8097->8088/tcp
```

### Step 6: Testing the Application
Finally, the app was tested using a curl command on App Server 1:
```bash
curl http://localhost:8097
```
<img width="1786" height="606" alt="Screenshot 2025-10-09 at 9 38 21 PM" src="https://github.com/user-attachments/assets/5a6c1dbc-7be0-49af-b7aa-dbd33285f752" />


If the containerized app is running properly, the command returns a valid response from the Python server.

### Result
The Python app was successfully Dockerized and deployed on App Server 1.
By containerizing the application, the Nautilus DevOps team achieved:
 - Easier deployment and portability
 - Consistent runtime environment
 - Simplified dependency management

### Summary
| Step | Description               | Command                                                                    |
| ---- | ------------------------- | -------------------------------------------------------------------------- |
| 1    | Navigate to app directory | `cd /python_app`                                                           |
| 2    | Create Dockerfile         | `vi Dockerfile`                                                            |
| 3    | Build Docker image        | `docker build -t nautilus/python-app .`                                    |
| 4    | Run Docker container      | `docker run -d --name pythonapp_nautilus -p 8097:8088 nautilus/python-app` |
| 5    | Test app                  | `curl http://localhost:8097`                                               |
