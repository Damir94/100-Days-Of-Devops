# Day 20 of 100 Days of DevOps - Deploying a PHP-FPM 8.1 App with Nginx on Custom Port

The Nautilus application development team is planning to launch a new PHP-based application, 
which they want to deploy on Nautilus infra in Stratos DC. 
The development team had a meeting with the production support team and they have shared 
some requirements regarding the infrastructure.




1️. Installed & Configured Nginx

sudo yum install -y nginx

<img width="755" height="117" alt="Screenshot 2025-09-13 at 10 22 56 AM" src="https://github.com/user-attachments/assets/576b9e6d-5c7f-47fb-abfb-1ddf681832fc" />

sudo vi /etc/nginx/nginx.conf

Inside the server { ... } block, configure it like this:

<img width="686" height="354" alt="Screenshot 2025-09-13 at 10 31 49 AM" src="https://github.com/user-attachments/assets/a300db8c-8d6f-4bd2-be57-04baa8b1a7db" />

**Reload Nginx:**

sudo systemctl reload nginx

2️. Installed PHP-FPM 8.1

# Enabled PHP 8.1 module

sudo yum install -y epel-release
sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
sudo yum module reset php -y
sudo yum module enable php:remi-8.2 -y

# Installed PHP and PHP-FPM

sudo yum install -y php php-fpm

Configure PHP-FPM socket:

sudo mkdir -p /var/run/php-fpm

sudo vi /etc/php-fpm.d/www.conf

Ensured these settings:

listen = /var/run/php-fpm/default.sock

listen.owner = nginx

listen.group = nginx

listen.mode = 0660

<img width="689" height="250" alt="Screenshot 2025-09-13 at 10 39 55 AM" src="https://github.com/user-attachments/assets/98c346e5-a3ff-452a-828d-d64446d328eb" />

Restart PHP-FPM:

sudo systemctl enable --now php-fpm
sudo systemctl enable --now nginx

sudo systemctl restart php-fpm
sudo systemctl restart nginx

4. Test the Setup
From jump host:

curl http://stapp02:8099/index.php

Expected output: Welcome to xFusionCorp Industries!

<img width="1262" height="576" alt="Screenshot 2025-09-13 at 10 42 04 AM" src="https://github.com/user-attachments/assets/41bb017c-5306-4313-b79d-86f06373519f" />


## Business Value Delivered
•	Developer Alignment: Infrastructure matched dev team’s runtime (PHP 8.1) → fewer bugs at deployment.

•	Security & Flexibility: Running on custom port avoids conflicts, supports traffic shaping.

•	Performance: Unix socket communication ensures lower latency & higher throughput.

•	Operational Excellence: Documented, tested workflow supports repeatable deployments across app servers.

## Key Learning (Day 20 Reflection)
This task deepened my understanding of:

•	Binding services to non-standard ports.

•	Why version control in runtime environments is critical for stability.

•	The role of PHP-FPM sockets in modern Nginx/PHP architectures.

•	Thinking like a DevOps engineer bridging business & technical needs.
