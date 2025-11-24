# 100 Days of Devops - Day 91: File & ACL Management Using Ansible

## Overview
This project contains an Ansible playbook designed to automate the creation of specific files and the management of Access Control Lists (ACLs) on all application servers in the Stratos Datacenter (DC).
The Nautilus DevOps team requires these files to be owned strictly by the root user, while granting app-specific users or groups additional permissions using ACLs.

## Objectives
1. The Ansible playbook accomplishes the following:
2. Creates required files on the respective app servers.
3. Ensures all files are owned by root:root.
4. Applies ACL permissions to specific users or groups.
5. Uses a centralized inventory for managing all target hosts.
6. Ensures full automation with no manual intervention.


## Files Managed
| App Server | File Path                | ACL Entity | Type  | Permission |
| ---------- | ------------------------ | ---------- | ----- | ---------- |
| stapp01    | `/opt/itadmin/blog.txt`  | tony       | group | r          |
| stapp02    | `/opt/itadmin/story.txt` | steve      | user  | rw         |
| stapp03    | `/opt/itadmin/media.txt` | banner     | group | rw         |


## Playbook Location
Place the playbook at:
```swift
/home/thor/ansible/playbook.yml
```
```yaml
---
- name: Manage files and ACLs on app servers
  hosts: all
  become: yes
  tasks:

    # Task for App Server 1
    - name: Create blog.txt on app server 1
      file:
        path: /opt/itadmin/blog.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp01"

    - name: Set ACL for group tony on blog.txt
      acl:
        path: /opt/itadmin/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present
      when: inventory_hostname == "stapp01"

    # Task for App Server 2
    - name: Create story.txt on app server 2
      file:
        path: /opt/itadmin/story.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp02"

    - name: Set ACL for user steve on story.txt
      acl:
        path: /opt/itadmin/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present
      when: inventory_hostname == "stapp02"

    # Task for App Server 3
    - name: Create media.txt on app server 3
      file:
        path: /opt/itadmin/media.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp03"

    - name: Set ACL for group banner on media.txt
      acl:
        path: /opt/itadmin/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
      when: inventory_hostname == "stapp03"
```
## How the Playbook Works
The playbook uses conditional execution (when:) to ensure each task runs only on the correct server. For example:
  - Only App Server 1 creates blog.txt and applies ACL for group tony.
  - Only App Server 2 creates story.txt and applies ACL for user steve.
  - Only App Server 3 creates media.txt with ACL for group banner.
The file module is used to create empty files and manage ownership.
The acl module applies fine-grained permission control not possible through standard Linux file modes.

An inventory file must already exist in:
```swift
/home/thor/ansible/inventory
```
## How to Run the Playbook

From the jump host, run:
```
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```

### Why ACLs?
ACLs allow more flexible permission management beyond the traditional owner/group/other model.
Using ACLs ensures:
  - Root keeps full control
  - App-specific users get exactly the permissions they need
  - No insecure permission escalation occurs

## Conclusion
This playbook provides a clean, automated, and scalable approach to managing file permissions across multiple servers in Stratos DC. 
By leveraging ACLs and Ansible logic, it ensures consistency, security, and maintainability across environments.
