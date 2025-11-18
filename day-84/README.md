# 100 Days of Devops - Day 82: Ansible Playbook for Copying Files to Stratos DC Application Servers
This project contains an Ansible setup used to copy a file from the jump host to all application servers in the Stratos Datacenter (Stratos DC).
It includes:
  -  An inventory file specifying all application servers.
  -  A playbook that copies the required index.html file to each server.

This setup is typically used in DevOps automation labs such as KodeKloud.

### Directory Structure
```swift
/home/thor/ansible/
├── inventory
└── playbook.yml
```
### Inventory File
The inventory file (inventory) defines all managed nodes (application servers).
Each server entry includes its IP address and SSH login details.
```ini
[apps]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
Group: apps

This group holds all target application servers.
The playbook runs against this group so that all servers receive the file.

### Playbook File
The playbook (playbook.yml) is responsible for copying the file:
  - Source: /usr/src/itadmin/index.html (on the jump host)
  - Destination: /opt/itadmin/index.html (on each app server)
```yaml
---
- name: Copy index.html to all application servers
  hosts: apps
  become: yes

  tasks:
    - name: Copy itadmin index.html
      copy:
        src: /usr/src/itadmin/index.html
        dest: /opt/itadmin/index.html
```
What this playbook does:
1. Connects to all servers in the apps group.
2. Elevates privileges using become: yes (required to write to /opt).
3. Copies the HTML file to /opt/itadmin/ on each server.
4. Creates the target directory automatically if it does not exist.

Running the Playbook

From the /home/thor/ansible/ directory, run:
```bash
ansible-playbook -i inventory playbook.yml
```
Expected output:
```javascript
stapp01 | SUCCESS => ...
stapp02 | SUCCESS => ...
stapp03 | SUCCESS => ...
```
### Summary

This Ansible automation:
  - Defines all application servers in a simple inventory.
  - Uses a secure, repeatable playbook to distribute a file across the environment.
  - Provides a consistent approach to configuration management within Stratos DC.
