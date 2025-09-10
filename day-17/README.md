# Day 17 – PostgreSQL Setup for Application Deployment


# Project Story
The Nautilus development team planned to deploy a new application requiring PostgreSQL. As part of the DevOps/Infra team, my role was to prepare the database server by:
1.	Creating a dedicated database user.
2.	Creating a dedicated database.
3.	Assigning full permissions so the application team can use the database seamlessly.
During this task, I also learned an important real-world lesson: PostgreSQL was not installed on the jump_host, but on a separate database server. I had to SSH into the correct server before running database commands — exactly the kind of situational awareness needed in production environments.

## Business Need & Essence
1. Secure Access Control: Applications should not use the default postgres user. A dedicated database user (kodekloud_joy) was created for isolation and security.
2. Resource Separation: A separate database (kodekloud_db3) ensures application data is logically isolated.
3. Permission Management: Fine-grained privileges prevent unauthorized access while giving developers the freedom to use the database.

This directly supports business continuity, security, and compliance, while enabling developers to move faster.

## Steps to Implement
1. SSH into the Database Server (not jump_host)

ssh peter@stdb01

<img width="737" height="304" alt="Screenshot 2025-09-10 at 8 19 57 AM" src="https://github.com/user-attachments/assets/661ce5e4-9771-4b4c-b76c-f22875bf1263" />

This ensured being on the correct server where PostgreSQL is installed.

2. Logged into PostgreSQL as Superuser
sudo -i -u postgres
psql

4. Created a Database User
CREATE USER kodekloud_aim WITH PASSWORD 'BruCStnMT5';

5. Created a Database
CREATE DATABASE kodekloud_db3;

6. Granted Permissions
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db3 TO kodekloud_joy;

<img width="729" height="219" alt="Screenshot 2025-09-10 at 8 22 56 AM" src="https://github.com/user-attachments/assets/3f99efe2-8ad3-44a8-987f-3bb458fda8e4" />


## Takeaway & Learning Impact
1. Learned to provision PostgreSQL users & databases securely.
2. Understood the role of privilege management in DevOps workflows.
3. Reinforced that database readiness is a critical pre-requisite for application deployment.
4. Learned the importance of working on the correct server — a common real-world DevOps troubleshooting scenario.

This exercise reflects a common real-world task for DevOps engineers, bridging application development and infrastructure setup.

