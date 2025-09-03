# 🚀 100 Days of DevOps – Hands-On Projects & Challenges
This repository documents my 100-day DevOps journey through three real-world, end-to-end projects:

✅ Application Deployment (xfusion – Project Nautilus)
☸️ Container Orchestration (Kubernetes)
🔧 Infrastructure as Code (Terraform automation)

Each day includes code, documentation, and lessons learned from practical, business-oriented scenarios. The goal isn’t just to follow tutorials, but to simulate real operational challenges DevOps engineers face in production environments.

---

## 🧩 Projects Overview

### 1. ⚓ Project Nautilus – xFusionCorp Industries
> Simulating a real company DevOps role

**Project Nautilus** is the Naval subdivision of xFusionCorp Industries. It’s a mission-critical web application built to help naval forces make smarter procurement decisions for manned and unmanned maritime systems, while ensuring operational requirements, safety, and long-term cost-efficiency.

#### 🏗 Architecture:
- **Three-tier deployment** in a North American datacenter
  - **Client Tier**: Web browser interface
  - **Application Tier**: LAMP stack (Linux, Apache, MariaDB, PHP)
  - **Data Tier**: MariaDB for RDBMS
- **Load Balancing**: Nginx HTTP load balancer
- **Shared Services**:
  - NAS-based external storage
  - Secure SFTP for file transfer
  - Backup staging server
  - Jump server (SSH gateway for access control)

#### 🎯 DevOps Scope:
- Provisioning secure access (jump servers, SSH key management)
- Deploying and configuring a 3-tier application
- Setting up load balancing with Nginx
- Automating backups and storage
- Documenting every step with screenshots, CLI commands, and best practices

---

### 2. ☸ Kubernetes Hands-On Path

A deep-dive into Kubernetes through daily tasks covering:
- Pod, Service, and Deployment management
- ConfigMaps, Secrets, and Volumes
- Rolling updates and rollback strategies
- Helm chart creation and usage
- Troubleshooting and debugging

---

### 3. 🛠 Terraform Infrastructure Automation

Using Terraform to provision infrastructure in a cloud-agnostic, repeatable way. Tasks include:
- Writing modules for reusable infrastructure components
- Managing state files securely
- Integrating with providers like AWS, GCP
- Infrastructure testing and validation

---

## 📅 Daily Progress

Each day contains subfolders for the three projects.
Inside each, you’ll find:
- `README.md` – description, steps, learnings
- `/code` – actual scripts, manifests, configs
- `/screenshots` – visual proof and context

---

## 🎯 Why I’m Doing This

I’m doing this challenge to:

- Solidify my DevOps skills through **consistent, real-world practice**
- Build a **portfolio that demonstrates business impact**, not just theoretical knowledge
- Learn to structure and document technical work for **team handoff, recruiters, and future me**
- Challenge myself to improve in areas like:
  - Linux system administration
  - CI/CD and automation
  - Cloud provisioning
  - Monitoring and troubleshooting

---

## 📬 Get in Touch

If you're a recruiter, hiring manager, or fellow DevOps learner – feel free to reach out or follow along!

- 💼 [LinkedIn] https://www.linkedin.com/in/damir-abdurakhimov-b30408177/
- 📧 abdurakhimov.da@gmail.com
- 🌐 [GitHub]https://github.com/Damir94

---

> 📌 _“Infrastructure as code is more than automation – it’s a discipline, a mindset, and a roadmap to scalable systems.”_
