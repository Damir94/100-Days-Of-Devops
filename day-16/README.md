# Day 16 - Configuring an Nginx Load Balancer for High Availability

## The task

Day by day traffic is increasing on one of the websites managed by the Nautilus production support team. 
Therefore, the team has observed a degradation in website performance. Following discussions about this issue, 
the team has decided to deploy this application on a high availability stack i.e on Nautilus infra in Stratos DC. 
They started the migration last month and it is almost done, as only the LBR server configuration is pending. 
Configure LBR server as per the information given below:

1. Install nginx on LBR (load balancer) server.

 - sudo yum install -y nginx
   
<img width="1030" height="419" alt="Screenshot 2025-09-09 at 6 27 37 AM" src="https://github.com/user-attachments/assets/9a45ca19-62e0-4fc8-b126-b929d9e56d60" />

2. Enable and start Nginx:

 - sudo systemctl enable nginx
 - sudo systemctl start nginx

<img width="1035" height="208" alt="Screenshot 2025-09-09 at 6 28 42 AM" src="https://github.com/user-attachments/assets/a5728273-7bc9-4cf6-9f3c-a7176a1d124d" />

3. Configure load-balancing with the an http context making use of all App Servers.
   Ensure that you update only the main Nginx configuration file located at /etc/nginx/nginx.conf.
   
 - sudo vi /etc/nginx/nginx.conf

<img width="638" height="461" alt="Screenshot 2025-09-09 at 11 21 23 AM" src="https://github.com/user-attachments/assets/f5d1dd26-9644-4678-9ab1-1762909daa43" />

 ⚠️ Important: 
  - Do not change Apache configs on App servers.
  - Use the correct port that Apache is already serving (check with sudo ss -tulnp | grep httpd on app servers).
  - 
<img width="1029" height="517" alt="Screenshot 2025-09-09 at 6 31 53 AM" src="https://github.com/user-attachments/assets/5d2e89b4-4cc2-401d-9537-b119ecece7b8" />

4. Test and Reload Nginx

 - sudo nginx -t
 - sudo systemctl restart nginx

<img width="624" height="89" alt="Screenshot 2025-09-09 at 6 51 10 AM" src="https://github.com/user-attachments/assets/f5d1908f-ae3a-4117-a0f7-cab0f0e800d5" />

5. Ensure Apache is running on all App servers:

 - sudo systemctl status httpd

6. From the LBR server, check connectivity:

 - curl http://APP1_IP:PORT
 - curl http://APP2_IP:PORT

<img width="574" height="164" alt="Screenshot 2025-09-09 at 6 51 23 AM" src="https://github.com/user-attachments/assets/c2add106-93f4-45e4-8968-d97bc56d993b" />


