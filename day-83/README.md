# 100 Days of Devops - Day 83: Ansible Playbook & Inventory Update – Documentation
This document provides a clear walkthrough for updating the Ansible inventory and 
creating a playbook to manage tasks for App Server 3 in the Stratos Datacenter (Stratos DC). 
It is intended for DevOps engineers working on the jump host in the training environment.

Overview

A previous team member partially configured the Ansible setup on the jump host. Your task is to:

1. Update the inventory file to properly target App Server 3.
2. Create an Ansible playbook that creates an empty file on App Server 3.
3. Ensure the playbook runs successfully using the validation command:

```css
ansible-playbook -i inventory playbook.yml
```
This document describes how to complete the setup cleanly and correctly.

1. Updating the Inventory File

The inventory determines which managed nodes Ansible interacts with. For this task, we must configure Ansible to run only on App Server 3.

Edit or create the inventory file:
```swift
/home/thor/ansible/inventory
```
Add the following configuration:
```ini
[appserver3]
stapp03 ansible_host=172.16.238.12 ansible_user=tony ansible_ssh_pass=tony@123 ansible_become_pass=tony@123
```
### Explanation
  - [appserver3] – Defines a host group for App Server 3.
  - stapp03 – Hostname of App Server 3 in Stratos DC.
  - ansible_host – IP address of App Server 3.
  - ansible_user / ansible_ssh_pass – SSH credentials used by Ansible.
  - ansible_become_pass – Privilege escalation password (sudo).
This allows validation (and you) to target App Server 3 without additional flags.

2. Creating the Ansible Playbook

Next, create the following playbook:
```swift
/home/thor/ansible/playbook.yml
```
Add
```yaml
---
- name: Create file on App Server 3
  hosts: appserver3
  become: yes

  tasks:
    - name: Create empty file
      file:
        path: /tmp/file.txt
        state: touch
```
### What this playbook does
  - Targets the appserver3 group from the inventory.
  - Elevates privileges using become: yes.
  - Uses the Ansible file module to create an empty file located at:
```bash
/tmp/file.txt
```
This ensures the required file exists on App Server 3.

3. Running the Playbook

Once the inventory and playbook are ready, run:
```css
ansible-playbook -i inventory playbook.yml
```
If the setup is correct, you should receive a success message and the file /tmp/file.txt will be created on App Server 3.

### Summary
| Task                                                  | Status      |
| ----------------------------------------------------- | ----------- |
| Update inventory to target App Server 3               | ✅ Completed |
| Create Ansible playbook to create file                | ✅ Completed |
| Ensure playbook works with default validation command | ✅ Validated |

### Notes for Future Maintenance
  - Always verify hostnames and credentials when working in the Stratos DC environment.
  - Consider creating host variables files for cleaner inventories as environments grow.
  - Use YAML linting (yamllint) to keep playbooks clean and readable.
