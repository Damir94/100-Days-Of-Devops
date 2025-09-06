# Day 7 — Installing Ansible 4.9.0 on a Jump Host (100 Days of Cloud/DevOps)

As part of my 100 Days of DevOps Challenge, I’m exploring infrastructure automation tools and cloud-native practices.

Over the past few days, I’ve worked on Linux administration, AWS provisioning, and Kubernetes basics. Today, I stepped into the world of Configuration Management with Ansible.

## Project Background
The Nautilus DevOps team decided to adopt Ansible for automation and configuration management due to its simplicity and agentless nature.

For initial testing, we set up the Jump Host as the Ansible Controller to run playbooks and tasks against target servers.

## Steps Performed
1. Verified Python3 & pip3

python3 --version

<img width="896" height="129" alt="Screenshot 2025-09-06 at 7 36 46 AM" src="https://github.com/user-attachments/assets/7268788a-65a9-43c4-a23d-20690126df69" />

pip3 --version

<img width="947" height="100" alt="Screenshot 2025-09-06 at 7 37 41 AM" src="https://github.com/user-attachments/assets/5a18e9c1-af55-4530-bd38-a831b2e0e42b" />


Confirmed both were installed.

2. Installed Ansible 4.9.0

sudo pip3 install ansible==4.9.0

Used sudo to ensure Ansible is installed system-wide.

3. Verified Installation

ansible --version

Confirmed version 4.9.0 installed successfully.
$<img width="621" height="127" alt="Screenshot 2025-09-06 at 7 38 38 AM" src="https://github.com/user-attachments/assets/84417f05-2f3c-49fa-8b00-1d8d27dfc665" />

4. Verified Global Installation Path

To ensure that Ansible was installed in a system-wide location accessible to all users, I checked the binary directly from its installation path:

/usr/local/bin/ansible --version

The output confirmed that Ansible 4.9.0 was installed and ready for use.

Since /usr/local/bin is part of the default $PATH for all users on most Linux systems, this ensures global availability without additional configuration.

<img width="739" height="188" alt="Screenshot 2025-09-06 at 7 39 24 AM" src="https://github.com/user-attachments/assets/e1f61592-0d9c-4390-85cd-55a26241f931" />


## Skills Demonstrated
Configuration Management Fundamentals - Installing and preparing Ansible.

Linux Administration - Managing global software installations.

Version Control in IaC Tools - Specifying exact versions for reproducibility.





