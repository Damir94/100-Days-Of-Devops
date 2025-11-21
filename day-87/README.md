# 100 Days of Devops - Day 87: Ansible Automation for Installing Samba on App Servers

This documentation explains how to set up an Ansible environment to automatically install the Samba package on all application servers in the Stratos Datacenter.

The Nautilus Application Development team requires Samba to be installed as a prerequisite for testing certain applications. This guide walks through:
  - Creating an inventory file
  - Writing an Ansible playbook
  - Running the playbook as user thor

### Project Structur
```swift
/home/thor/playbook/
├── inventory
└── playbook.yml
```
1. Create Ansible Inventory

The first step is defining the application servers Ansible will manage.
Create the inventory file:

File: /home/thor/playbook/inventory
```ini
[app]
stapp01
stapp02
stapp03
```
  - The [app] group contains all application servers
  - Make sure these hostnames match the actual servers in your environment.

2. Create Ansible Playbook

Next, create a playbook that installs the samba package on all app servers.

File: /home/thor/playbook/playbook.yml
```yaml
---
- name: Install samba package on all app servers
  hosts: app
  become: yes

  tasks:
    - name: Install samba
      yum:
        name: samba
        state: present
```
What this playbook does:
  - Runs on all hosts under the app group.
  - Uses become: yes to run tasks with elevated privileges.
  - Installs the Samba package using Ansible’s yum module.

3. Execute the playbook:
```bash
ansible-playbook -i inventory playbook.yml
```
## Conclusion

By following this guide, you have:
  - Defined a clean Ansible inventory
  - Automated Samba installation across multiple servers
  - Ensured infrastructure consistency using a repeatable playbook
This setup forms a foundation for future automation tasks within the Stratos Datacenter.
