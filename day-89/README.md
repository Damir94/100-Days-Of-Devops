# 100 Days of Devops - Day 89: Automating HTTPD Installation on Nautilus App Servers Using Ansible
This guide explains how to automate the installation and configuration of the httpd web server across all Nautilus application servers in Stratos DC using Ansible. 
The DevOps team uses this automation to ensure consistency, speed, and reliability across all environments.

## Overview
Developers requested that the httpd package be installed and running on all application servers. 
Instead of doing this manually, we are leveraging Ansible to install packages, start services, and maintain consistency across the infrastructure.

### Directory Structure
All files are located on the jump host under:
```swift
/home/thor/ansible/
```
### Inventory File
The inventory file lists all app servers in Stratos DC that the playbook will target:

File: /home/thor/ansible/inventory

Example:
```ini
[app]
stapp01
stapp02
stapp03
```
You may have different hostnames depending on the environment, but the group must be named app since the playbook references it.

### Playbook Overview
The playbook installs the httpd package on all app servers, ensures the service is started, and enables it to start automatically on system boot.

File: /home/thor/ansible/playbook.yml
```yaml
---
- name: Install and configure httpd on all app servers
  hosts: app
  become: yes

  tasks:
    - name: Install httpd package
      package:
        name: httpd
        state: present

    - name: Ensure httpd service is started and enabled
      service:
        name: httpd
        state: started
        enabled: yes
```
### What this playbook does:
1. Connects to all servers in the app group
2. Installs the httpd package
3. Starts the httpd service immediately
4. Enables httpd to automatically start on every reboot

### Running the Playbook

To execute the automation:
```bash
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```
You should see output indicating successful installation and service startup on each app server.

### Verification
After the run, you can verify the service status from the jump host:
```bash
ansible app -i inventory -m shell -a "systemctl status httpd"
```
### Conclusion
This automation ensures:
  - Consistent installation of httpd across all app servers
  - Automated service management
  - Faster, repeatable deployments
  - Reduced risk of human error
By using Ansible, the DevOps team can deliver infrastructure changes faster and more reliably.
