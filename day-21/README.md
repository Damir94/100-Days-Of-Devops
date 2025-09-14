# Day 21 of 100 Days of DevOps - Git Repository Setup on Storage Server
The Nautilus development team has provided requirements to the DevOps team for a new application development project, 
specifically requesting the establishment of a Git repository. 
Follow the instructions below to create the Git repository on the Storage server in the Stratos DC:

 - a. Utilize yum to install the git package on the Storage Server. 
 - b. Create a bare repository named /opt/news.git (ensure exact name usage).
   
## Steps & Implementation

1. Install Git on the Storage Server

sudo yum install -y git

<img width="773" height="168" alt="Screenshot 2025-09-13 at 8 32 21 PM" src="https://github.com/user-attachments/assets/58d64f6e-ed55-46c1-b5e2-3089a10fd956" />

2️. Create the Bare Repository

  - sudo mkdir -p /opt/news.git
  - sudo git init --bare /opt/news.git

<img width="743" height="288" alt="Screenshot 2025-09-13 at 8 33 02 PM" src="https://github.com/user-attachments/assets/b1eb54fa-fdd4-4209-91ce-ca5149881fe0" />


3️. Verify Repository Structure

ls -l /opt/news.git

<img width="496" height="241" alt="Screenshot 2025-09-13 at 8 33 50 PM" src="https://github.com/user-attachments/assets/1a6d2a5d-259f-4337-801b-75d495dd3b4c" />




