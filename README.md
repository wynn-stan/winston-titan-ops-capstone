# DevOps Infrastructure Automation & CI/CD Project

This project creates a DevOps setup using **AWS EC2**, **Ansible**, **Docker**, **Traefik**, and **GitHub Actions**. It automates the provisioning and configuration of a virtual machine, sets up essential services, and implements a CI/CD pipeline for a containerized application.

---

## 🚀 Project Overview

### 🛠️ Infrastructure Setup

1. **Provisioning a VM on AWS EC2**

   - A new EC2 instance was created to host the application infrastructure.

2. **SSH Configuration**

   - SSH config was set up locally to allow easy and secure access to the EC2 instance.

3. **Ansible Playbook**

   - An Ansible playbook was used to automate the configuration of the EC2 instance, consisting of the following roles:

   | Role         | Description                                                                           |
   | ------------ | ------------------------------------------------------------------------------------- |
   | `hostname`   | Sets a custom hostname (vps) on the VM                                                |
   | `essentials` | Installs base packages, enables services on boot, and sets up a bridge network        |
   | `security`   | Applies security configurations including `nftables` rules and allowed SSH ports      |
   | `kernel`     | Applies kernel tweaks (e.g., enables IP forwarding)                                   |
   | `docker`     | Installs and configures Docker                                                        |
   | `traefik`    | Installs and sets up Traefik as a load balancer and reverse proxy for Docker services |

4. **Local DNS Mapping**
   - Added the following entry to the local `/etc/hosts` file to map the custom domain to the VM:
     ```
     <vm_ip>  traefik.local.test
     ```

---

## ⚙️ CI/CD Pipeline

For another project ([Task Tracker Repo](https://github.com/wynn-stan/task-tracker)), I set up a simple CI/CD pipeline that handles build and deployment using Docker and GitHub Actions.

### 🧱 Components (template-repo-assets/)

- **Dockerfile**

  - Multi-stage build to optimize the final image size `(~230mb)` and performance.

- **docker-compose.yml**

  - Defines and runs the Docker container on the EC2 instance with appropriate configuration.

- **deploy.yml (GitHub Actions)**
  - Automates the deployment process via CI/CD. Triggered on push, it builds the Docker image, pushes it to DockerHub, and deploys it to the VM.

---

## 📌 Notes

- This is a basic setup, mostly meant to demonstrate provisioning, configuration, and deployment with minimal tools.
- Could be improved with monitoring/logging, better security, server backups, and more.
