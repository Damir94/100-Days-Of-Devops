# Ansible Playbook: Copy Files to All Application Servers

## Overview
The Nautilus DevOps team needs to automate the process of copying data from the jump host to all application servers in Stratos DC.
This task uses Ansible to ensure the same index.html file is deployed consistently across all application servers.

## Objective
Copy the file /usr/src/devops/index.html from the jump host to all application servers, placing it at /opt/devops on each target server.

Directory Structure
```swift
/home/thor/ansible/
├── inventory
└── playbook.yml
```
1. Inventory File
File: /home/thor/ansible/inventory
This file lists all managed nodes (application servers) that Ansible will connect to.
```ini
[app_servers]
stapp01 ansible_host=<IP_of_App_Server_1> ansible_user=tony ansible_ssh_pass=<password>
stapp02 ansible_host=<IP_of_App_Server_2> ansible_user=steve ansible_ssh_pass=<password>
stapp03 ansible_host=<IP_of_App_Server_3> ansible_user=banner ansible_ssh_pass=<password>
```
Replace the IPs and passwords with the actual connection details of each app server.
ansible_user and ansible_ssh_pass specify how Ansible will connect via SSH.

2. Playbook File
File: /home/thor/ansible/playbook.yml
This playbook defines the automation steps for copying the file.
```yaml
---
- name: Copy index.html file to all app servers
  hosts: app_servers
  become: yes
  tasks:
    - name: Copy index.html to /opt/devops
      copy:
        src: /usr/src/devops/index.html
        dest: /opt/devops/index.html
```
### Explanation
  - hosts: app_servers → Targets all servers under the [app_servers] group.
  - become: yes → Runs with elevated (sudo) privileges.
  - copy module → Handles file transfer from the jump host to the remote servers.
  - src → File path on the control node (jump host).
  - dest → Destination path on each managed node.

3. Running the Playbook
Execute the playbook using this command:
```bash
ansible-playbook -i /home/thor/ansible/inventory /home/thor/ansible/playbook.yml
```
This command will:
  - Connect to all application servers defined in the inventory.
  - Copy the index.html file to /opt/devops on each server.

4. Verification
SSH into any application server and verify:
```bash
ls -l /opt/devops/index.html
```
If the file exists, the playbook executed successfully 

<img width="1690" height="647" alt="Screenshot 2025-10-26 at 11 00 33 AM" src="https://github.com/user-attachments/assets/c2139984-c55b-4fcd-a9e7-e45f10c5c7ee" />

5. Notes
  - Ensure passwordless SSH or valid credentials are set for all target servers.
  - If /opt/devops doesn’t exist, the copy module automatically creates it.
  - This playbook can be reused for distributing other static files as well.
