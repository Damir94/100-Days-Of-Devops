# 100 Days of Devops - Day 82: Ansible Inventory & Playbook Setup for Stratos DC (App Server 2)

This guide documents the setup and usage of an Ansible inventory and playbook designed for the Nautilus DevOps Team. 
The goal is to execute Ansible automation on App Server 2 in the Stratos Datacenter (Stratos DC) using a well-structured inventory file and a test playbook.

## Overview
The Nautilus DevOps team maintains several Ansible playbooks under
/home/thor/playbook/ on the jump host.
To run these playbooks against App Server 2, an inventory file must be created with the correct configuration and host variables.

This README covers:
  - Creating an INI-based Ansible inventory
  - Adding App Server 2 with the proper hostname and variables
  - Ensuring Ansible connects successfully using SSH and privilege escalation
  - Running the playbook without extra CLI arguments

## Directory Structure
```swift
/home/thor/playbook/
├── inventory
└── playbook.yml
```
The inventory file is created manually.
The playbook.yml file contains the tasks you want to test on App Server 2.

## Inventory File Configuration

Create the inventory file at:
```swift
/home/thor/playbook/inventory
```
This file must be in INI format and must reference App Server 2 using the correct hostname as defined in the Stratos DC wiki.

## Inventory Entry for App Server 2
```makefile
[app2]
stapp02 ansible_host=stapp02 \
ansible_user=tony \
ansible_ssh_pass=Ir0nM@n \
ansible_become_pass=Ir0nM@n \
ansible_python_interpreter=/usr/bin/python3
```
## Explanation of Variables
| Variable                     | Description                                   |
| ---------------------------- | --------------------------------------------- |
| `ansible_host`               | The hostname or IP Ansible connects to        |
| `ansible_user`               | SSH user (default for App Server 2 is `tony`) |
| `ansible_ssh_pass`           | SSH password                                  |
| `ansible_become_pass`        | Privilege escalation (sudo) password          |
| `ansible_python_interpreter` | Ensures Python3 is used during execution      |

These values ensure that Ansible can authenticate, run commands, and execute modules on the managed node.

## Running the Playbook
Validation will run the playbook using:
```css
ansible-playbook -i inventory playbook.yml
```
## Example playbook.yml
Below is a simple example playbook you can use for testing:
```yaml
---
- name: Test Ansible on App Server 2
  hosts: app2
  become: yes

  tasks:
    - name: Create a test file
      file:
        path: /tmp/testfile.txt
        state: touch
```

## Summary
By following this guide, you will:
  - Create a valid INI-style inventory for App Server 2
  - Configure all required connection variables
  - Ensure the playbook can be executed directly using ansible-playbook
  - Maintain a clean and understandable directory structure
This setup is ideal for testing Ansible playbooks in the Stratos DC environment and aligns with best practices used by the Nautilus DevOps team.
