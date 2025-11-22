# 100 Days of Devops – Day 88: Deploying a Simple Apache Web Server Using Ansible
## Overview

This project provides an Ansible playbook that installs and configures a simple Apache (httpd) web server on all application servers in the Stratos Datacenter.
It also deploys a sample HTML page using automated configuration management practices.

The goal is to standardize how the Nautilus DevOps team manages web server provisioning and ensure all environments receive consistent setup and content.

## Objectives

This playbook performs the following actions:

Installs the httpd package on all app servers.

Ensures the httpd service is started and enabled on boot.

Creates or updates /var/www/html/index.html with predefined content using the blockinfile module.

Sets appropriate ownership (apache:apache) and permissions (0755) on the web page.

This automation ensures repeatability, reliability, and ease of deployment across multiple servers.

## Repository Structure
```bash
/home/thor/ansible/
│
├── inventory            # Inventory file containing app servers
└── playbook.yml         # Main Ansible playbook
```
## Prerequisites

Ansible installed on the jump host

SSH connectivity to all app servers

Inventory file created under /home/thor/ansible/inventory

Sudo privileges for managing packages and services

Target servers running a Linux OS that supports httpd (CentOS/RHEL)

##  How to Run the Playbook

Navigate to the Ansible directory:
```bash
cd /home/thor/ansible
```

Execute the playbook:
```bash
ansible-playbook -i inventory playbook.yml
```

After execution, verify Apache is running:
```bash
systemctl status httpd
```

View the deployed webpage:
```bash
cat /var/www/html/index.html
```
## Playbook Explanation

Below is the playbook used in this project:
```yaml
---
- name: Install and configure httpd on all app servers
  hosts: appservers
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

    - name: Add sample content to index.html
      blockinfile:
        path: /var/www/html/index.html
        create: yes
        block: |
          Welcome to XfusionCorp!

          This is  Nautilus sample file, created using Ansible!

          Please do not modify this file manually!

    - name: Set ownership for index.html
      file:
        path: /var/www/html/index.html
        owner: apache
        group: apache
        state: file

    - name: Set permissions for index.html
      file:
        path: /var/www/html/index.html
        mode: '0755'
```
## Why Use Ansible for This Task?

Using Ansible ensures:

 No manual configuration

 Consistent environment setup

 Idempotency — running the playbook multiple times will not duplicate or break configurations

 Easy scaling to new servers

 Clear auditability and documentation

This is especially important for large infrastructure environments such as Stratos DC.

## Future Enhancements

You may extend this project by:

Adding templates using jinja2

Deploying full web applications

Adding custom roles for better structure

Integrating CI/CD for continuous deployment

Running health checks after deployment

## Conclusion

This project demonstrates how Ansible can simplify and automate web server provisioning.
By maintaining consistent configurations and enforcing best practices, the Nautilus DevOps team can support reliable application testing environments across all app servers.

