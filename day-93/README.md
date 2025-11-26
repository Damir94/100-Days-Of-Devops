# 100 Days of Devops - Day 93: Ansible Conditional File Deployment – Documentation

## Overview

This project demonstrates how the Nautilus DevOps team uses Ansible conditionals to perform host-specific automation tasks during their cloud migration.
The goal is to train team members to understand and utilize Ansible’s when conditionals for fine-grained control over tasks.

In this example, different application servers receive different files, each with unique ownership and permissions—all using a single Ansible playbook.

## Inventory

The inventory file (already provided) is located at:
```swift
/home/thor/ansible/inventory
```
It contains all Stratos DC app servers, such as:
```nginx
stapp01
stapp02
stapp03
```

## Objective

Copy three different files from the jump host to three different application servers based on the hostname.
| App Server | Source File (Jump Host)  | Destination Path     | Owner  | Group  | Mode |
| ---------- | ------------------------ | -------------------- | ------ | ------ | ---- |
| stapp01    | `/usr/src/dba/blog.txt`  | `/opt/dba/blog.txt`  | tony   | tony   | 0655 |
| stapp02    | `/usr/src/dba/story.txt` | `/opt/dba/story.txt` | steve  | steve  | 0655 |
| stapp03    | `/usr/src/dba/media.txt` | `/opt/dba/media.txt` | banner | banner | 0655 |
These tasks must be triggered only when the correct server is targeted.

### Playbook: playbook.yml

The playbook is stored at:
```swift
/home/thor/ansible/playbook.yml
```

## Full Playbook
```yaml
---
- name: Copy files to different app servers using conditionals
  hosts: all
  become: yes

  tasks:

    - name: Copy blog.txt to App Server 1
      copy:
        src: /usr/src/dba/blog.txt
        dest: /opt/dba/blog.txt
        owner: tony
        group: tony
        mode: '0655'
      when: inventory_hostname == "stapp01"

    - name: Copy story.txt to App Server 2
      copy:
        src: /usr/src/dba/story.txt
        dest: /opt/dba/story.txt
        owner: steve
        group: steve
        mode: '0655'
      when: inventory_hostname == "stapp02"

    - name: Copy media.txt to App Server 3
      copy:
        src: /usr/src/dba/media.txt
        dest: /opt/dba/media.txt
        owner: banner
        group: banner
        mode: '0655'
      when: inventory_hostname == "stapp03"
```
## How It Works

Ansible’s when conditional ensures that each task runs only on the appropriate server:
  - inventory_hostname == "stapp01"
  → Only App Server 1 executes the blog.txt copy
  - inventory_hostname == "stapp02"
  → Only App Server 2 executes the story.txt copy
  - inventory_hostname == "stapp03"
  → Only App Server 3 executes the media.txt copy

This makes the playbook efficient, readable, and scalable—ideal for training new team members.

## Running the Playbook

From the jump host:
```bash
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```

Key Concepts Learned
Ansible Conditionals (when:)
  - Conditionals allow tasks to execute only when specific criteria are met.

Host-specific automation
  - Each server receives exactly what it needs—nothing more, nothing less.

Centralized Playbook
  - Instead of maintaining multiple playbooks, all logic is kept in one file.

## Summary
This exercise teaches how to:
  - Use Ansible’s powerful conditional logic
  - Apply host-specific file operations
  - Manage ownership, permissions, and file delivery
  - Build clean, scalable Ansible automation
This pattern is widely used in real-world DevOps environments where different servers have unique responsibilities.
