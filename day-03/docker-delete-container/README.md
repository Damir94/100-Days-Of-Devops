# Removal of kke-container on Application Server 2
## Overview
During initial development, a temporary container named kke-container was created on Application Server 2 for testing purposes by one of the Nautilus project developers.
Since the container was no longer needed, the DevOps team performed a clean-up to remove it from the system and maintain a tidy environment.

## Objective
Delete the existing kke-container from App Server 2 to free up resources and maintain container hygiene.

## Environment Details
  - Server: Application Server 2
  - Base OS: Linux
  - Container Engine: Docker
  - Container Name: kke-container

## Steps to Remove the Container
1. Connect to Application Server 2
Access the target server through the jump host:
```bash
ssh steve@stapp02
```
(Username may differ depending on environment setup.)

2. List Existing Containers
Check all running and stopped containers:
```bash
sudo docker ps -a
```
This confirms the presence of the container kke-container.

3. Stop the Container (if running)
If the container is still active, stop it first:
```bash
sudo docker stop kke-container
```
4. Remove the Container
Once stopped, remove it permanently:
```bash
sudo docker rm kke-container
```
5. Verify Deletion
Confirm that the container is successfully deleted:
```bash
sudo docker ps -a
```
Expected output should no longer list kke-container.

## Validation
To ensure the environment is clean:
```bash
sudo docker container ls -a
```
No record of kke-container should be found.

## Conclusion
The kke-container was successfully removed from Application Server 2 as part of routine clean-up and maintenance.
This step helps maintain a minimal and controlled Docker environment across all application servers in the Stratos DC infrastructure.
