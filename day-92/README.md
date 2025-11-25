# 100 Days of Devops - Day 92: HTTPD Role — Installation & Template Deployment (Ansible)

This project contains an Ansible role that installs and configures the Apache HTTPD web server on target hosts.
As part of the configuration, the role also deploys a dynamic index.html file using a Jinja2 template.

The template automatically inserts the server’s hostname using Ansible facts, allowing consistent and flexible deployment across multiple application servers.

## Project Objectives
  - This role performs:
  - Installation of the httpd web server
  - Deployment of a dynamic Jinja2-based index.html file
  - Ensures correct file permissions and ownership
  - Allows flexible use across multiple servers without hardcoding values
  - This setup is ideal for teams training in automation or building uniform configuration for multiple environments.

## Directory Structure
```pgsql
ansible/
├── inventory
├── playbook.yml
└── role/
    └── httpd/
        ├── tasks/
        │   └── main.yml
        ├── templates/
        │   └── index.html.j2
        └── defaults/
```
Key Components
  - tasks/main.yml — Contains tasks to install HTTPD and deploy the index file.
  - templates/index.html.j2 — Jinja2 template used to create a dynamic landing page.
  - inventory — List of application servers.
  - playbook.yml — Executes the httpd role on App Server 3.

## Jinja2 Template: Dynamic HTML Content

The template file:
```nginx
This file was created using Ansible on {{ inventory_hostname }}
```
This ensures each server gets a customized homepage showing its own hostname.

Why is this useful?
  - Makes debugging easier
  - Confirms deployments are running on the correct host
  - Avoids hardcoding hostnames

## Key Role Task: Deploy Template

Inside tasks/main.yml, the role includes:
```yaml
- name: Deploy index.html template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: '0744'
```
What this task ensures:
  - Correct deployment of the template
  - Ownership assigned dynamically to the correct sudo user (e.g., tony, steve, banner)
  - Proper file permissions (0744)
  - No hardcoded values → ensures reusability

## Running the Playbook

Execute the playbook with:
```bash
ansible-playbook -i ~/ansible/inventory ~/ansible/playbook.yml
```
This will:
  - Connect to App Server 3
  - Run the httpd role
  - Install and configure Apache
  - Deploy index.html dynamically
