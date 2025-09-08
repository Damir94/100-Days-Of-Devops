# Day 10 of 100 Days of DevOps: Secure Nginx Deployment
Continuing my 100 Days of DevOps journey, today I worked on a real-world server preparation task, setting up a secure Nginx server with SSL on App Server 3 in Stratos Datacenter.

## Project Overview
The xFusionCorp Industries SysAdmin team required a production server to be made ready for application deployment. The key requirements included:

•	Installing & configuring Nginx.

•	Deploying a self-signed SSL certificate.

•	Creating a default index.html file.

•	Testing access securely from another server.

## Steps & Essence
1.	Installed Nginx on App Server 3

-Command: sudo yum install nginx -y

Essence: Web servers form the backbone of web applications. Getting Nginx running is the foundation.

<img width="988" height="235" alt="Screenshot 2025-09-08 at 11 25 26 AM" src="https://github.com/user-attachments/assets/15b36d88-7590-4ae4-8654-1ae7f26e3224" />


2.	Moved SSL certificates to secure location

-Location: /etc/nginx/ssl/

Essence: SSL certificates enforce encrypted communication, critical for business trust and compliance.

<img width="569" height="174" alt="Screenshot 2025-09-08 at 11 27 36 AM" src="https://github.com/user-attachments/assets/a5451760-6716-475c-b326-2921b2fef993" />

3.	Configured Nginx for HTTPS

-Config file: /etc/nginx/conf.d/nautilus.conf

Essence: Ensures the application will be accessible securely, aligning with modern security best practices.

<img width="530" height="249" alt="Screenshot 2025-09-08 at 11 30 10 AM" src="https://github.com/user-attachments/assets/6050d014-1c1d-4d38-9f23-fa67ef1c24a0" />

4.	Created index.html with “Welcome!”

-Location: /usr/share/nginx/html/

-Essence: Provides a visible validation of deployment success for users and stakeholders.

<img width="636" height="133" alt="Screenshot 2025-09-08 at 11 30 41 AM" src="https://github.com/user-attachments/assets/e5881f84-cfc0-405c-b50e-c4ec97589d11" />

<img width="768" height="85" alt="Screenshot 2025-09-08 at 11 32 22 AM" src="https://github.com/user-attachments/assets/994eb686-2e43-4c1d-8e05-5460bfb21ded" />


5. Tested from Jump Host using curl

-Command: curl -Ik https://172.16.238.11/

<img width="471" height="291" alt="Screenshot 2025-09-08 at 11 33 18 AM" src="https://github.com/user-attachments/assets/f029d918-3430-49e9-b70f-07286b38341e" />


-Essence: Final confirmation of end-to-end connectivity and secure readiness for deployment.

## Key Learnings & Business Impact
1. Learned how to deploy and secure web servers.
2. Understood the importance of SSL for data security & compliance.
3. Gained experience in end-to-end testing of server readiness.

## Recruiter takeaway 
This demonstrates my ability to handle real-world server provisioning tasks that are essential in DevOps and cloud environments.
