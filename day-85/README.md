# 100 Days of Devops - Day 85: File Creation & Ownership Management Using Ansible in Stratos DC

This guide walks through an Ansible automation task performed by the Nautilus DevOps team to create a file 
on multiple application servers in Stratos DC, set the correct permissions, and ensure each server has a unique owner for the file.

### Task Objective

Using Ansible:

1. Create an inventory file listing all application servers.
2. Create an Ansible playbook to:
    - Create a blank file /tmp/webdata.txt on all app servers.
    - Set file permissions to 0655.
    - Assign different owners to the file on each server:
        - App Server 1 → tony
        - App Server 2 → steve
        - App Server 3 → banner
Because these users may not exist on the servers, the playbook must create them when needed.

### 1. Inventory File
Create the inventory file at:
```bash
~/playbook/inventory
```
Add all application servers:
```ini
[app_servers]
stapp01
stapp02
stapp03
```
### 2. Ansible Playbook
Create the playbook at:
```bash
~/playbook/playbook.yml
```
Paste the following:
```yaml
---
- name: Create webdata file on app servers
  hosts: app_servers
  become: yes

  vars:
    file_owner: "{{ 'tony' if inventory_hostname == 'stapp01' else
                   'steve' if inventory_hostname == 'stapp02' else
                   'banner' }}"

  tasks:
    - name: Ensure the required user exists
      user:
        name: "{{ file_owner }}"

    - name: Create blank file with correct permissions and owner
      file:
        path: /tmp/webdata.txt
        state: touch
        mode: '0655'
        owner: "{{ file_owner }}"
        group: "{{ file_owner }}"
```
### How It Works
Dynamic Owner Assignment

Using conditional Jinja expressions, the playbook automatically chooses the correct owner based on the server hostname.
| Server  | Owner  |
| ------- | ------ |
| stapp01 | tony   |
| stapp02 | steve  |
| stapp03 | banner |

User Creation

If the required user doesn’t already exist, Ansible creates it before setting file ownership.

### File Creation + Permissions
  - The file module ensures:
  - File exists (empty)
  - Mode is set to 0655
  - Owner and group are configured per server

### 3. Running the Playbook
From the playbook directory:
```bash
cd ~/playbook
ansible-playbook -i inventory playbook.yml
```
You should see successful tasks for all three app servers.

### Final Result
After execution:
  - /tmp/webdata.txt exists on all app servers
  - The file has permissions 0655
  - Owner and group match the required user per server
  - The users exist and were created automatically
