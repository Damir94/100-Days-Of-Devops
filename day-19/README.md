# Day 19 of 100 Days of DevOps- Hosting Multiple Static Websites on Apache
xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter. 
The development of these websites is still in-progress, but we want to get the servers ready. 
Please perform the following steps to accomplish the task:

## Business Essence
For companies, reducing time-to-market is critical. By provisioning Apache in advance and configuring it to serve multiple static websites on a custom port (6400), we ensured:
-Faster deployment cycles once dev work is done.

-Security & compliance by using a non-standard port.

-Clear separation of environments for each site (news and games).

-Business continuity — operations don’t wait on development.

This highlights the DevOps principle of collaboration between Dev and Ops, where infra is prepared ahead of code readiness.

## Steps I Took

1. Installed Apache Web Server on App Server 1

sudo yum install httpd -y

<img width="1049" height="173" alt="Screenshot 2025-09-12 at 10 36 02 AM" src="https://github.com/user-attachments/assets/d9133cbf-ce8f-4a18-8ca6-b881e8ac847a" />


2. Configured Apache to Listen on Port 8084

sudo vi /etc/httpd/conf/httpd.conf

<img width="257" height="59" alt="Screenshot 2025-09-12 at 10 37 09 AM" src="https://github.com/user-attachments/assets/93fc028f-ad1f-47b0-a517-69edd31765a7" />


3. Copy Website Files from jump_host to App Server 1

scp -r /home/thor/blog /home/thor/games  tony@appserver1:/tmp/

<img width="1053" height="192" alt="Screenshot 2025-09-12 at 10 38 52 AM" src="https://github.com/user-attachments/assets/a6848e00-ae38-48ed-b1e3-30efe4fd6dc2" />


5. Move them into Apache document root:

sudo mv /tmp/blog /var/www/html/
sudo mv /tmp/games /var/www/html/

<img width="1055" height="225" alt="Screenshot 2025-09-12 at 10 39 53 AM" src="https://github.com/user-attachments/assets/ffd29568-e601-486c-bd2d-eb44cf96e033" />

6. Configure Apache for Blog and Games:
   Create a configuration file:
   
   sudo vi /etc/httpd/conf.d/websites.conf

   <img width="451" height="358" alt="Screenshot 2025-09-12 at 10 41 09 AM" src="https://github.com/user-attachments/assets/ae06834f-7cae-47f5-914a-32c440d90a15" />


7. Restarted Apache

sudo systemctl restart httpd

sudo systemctl enable httpd

<img width="1063" height="84" alt="Screenshot 2025-09-12 at 10 42 28 AM" src="https://github.com/user-attachments/assets/51f7ff47-61a9-4ae7-b57c-7a5d18d96a50" />


7. Test Websites

curl http://localhost:8084/blog/
curl http://localhost:8084/games/

<img width="571" height="415" alt="Screenshot 2025-09-12 at 10 42 37 AM" src="https://github.com/user-attachments/assets/c9f781b1-70c6-4a8f-b0ab-57986690d09b" />

## Key Takeaways
•	Learned how to configure Apache to serve multiple sites on a custom port.

•	Solved a real-world permissions issue by staging files in /tmp/ and moving with sudo.

•	Reinforced the DevOps principle of infra readiness, ensuring operations and dev teams can work in parallel.
