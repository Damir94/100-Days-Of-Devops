# Day 11 – Deploying Java Web Application on Tomcat

**Part of my [100 Days of DevOps] challenge**

## Context
For Day 11, I simulated a real-world Java application deployment scenario. The Nautilus DevOps team had completed the beta version of a Java web application and decided to host it on Tomcat for internal testing.

## Objective
- Install Tomcat on App Server 1

- Configure Tomcat to run on a custom port (3001)

- Deploy `ROOT.war` so the app runs directly on the base URL

- Verify deployment from the jump host

## Tools & Environment

- **Tomcat 9** (application server)

- **Java WAR** deployment

- **Linux server management**

- **SSH & SCP** for file transfer

## Step-by-Step Solution

1. **SSH into App Server 1**

ssh tony@stapp01

Installed Tomcat

sudo yum install -y tomcat tomcat-webapps tomcat-admin-webapps

<img width="1053" height="173" alt="Screenshot 2025-09-04 at 6 48 24 AM" src="https://github.com/user-attachments/assets/6679e0b8-9621-4ef5-8d9b-5d6dbd10298c" />

<img width="1035" height="831" alt="Screenshot 2025-09-04 at 6 50 37 AM" src="https://github.com/user-attachments/assets/4754b4e2-36ca-4f94-a518-abcc1afc607d" />


2. **Change Tomcat Port**

sudo vi /etc/tomcat/server.xml

Changed 8080 → 3003.

<img width="1035" height="285" alt="Screenshot 2025-09-04 at 6 52 12 AM" src="https://github.com/user-attachments/assets/6cc70a35-0f5f-465f-984e-4253a0c81ea3" />

3. **Start and Enable Tomcat**

sudo systemctl start tomcat

sudo systemctl enable tomcat

4. **Deploy ROOT.war**

scp /tmp/ROOT.war tony@stapp01:/tmp

sudo mv /tmp/ROOT.war /var/lib/tomcat/webapps/

5. **Restart Tomcat**

sudo systemctl restart tomcat

<img width="623" height="129" alt="Screenshot 2025-09-04 at 6 57 01 AM" src="https://github.com/user-attachments/assets/3f1b8f09-0194-4766-8a8f-0b22579c2480" />

<img width="1034" height="340" alt="Screenshot 2025-09-04 at 6 54 15 AM" src="https://github.com/user-attachments/assets/eaa1e08b-ac80-42f4-bfa7-8f2409d07b59" />

6. **Test Application**

curl http://stapp01:3001

<img width="755" height="391" alt="Screenshot 2025-09-04 at 6 58 41 AM" src="https://github.com/user-attachments/assets/637f8d5a-dfae-4d6f-93bd-01aa96884212" />

## Outcome
Successfully deployed a Java WAR file to Tomcat.

Configured custom port without breaking service.

Verified application runs from the base URL with no additional path.

## Key Takeaways
Tomcat configuration flexibility enables quick adaptation for different environments.

WAR deployment to the ROOT context simplifies application access for users.

Command-line deployment skills are crucial for DevOps engineers in hybrid environments.
