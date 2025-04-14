# STS Capstone Project Documentation

## 🚀 Project Overview

This project creates a simple DevOps setup using **AWS EC2, Ansible, Docker, Traefik, Prometheus, Grafana, Alertmanager, and GitHub Actions**. It serves to demonstrate a DevOps workflow incorporating infrastructure as code, configuration management, containerization, service orchestration, monitoring, and CI/CD.

## 🚀 Architecture Overview

The architecture consists of:

- **AWS EC2 Instance**: A Cloud VM that hosts all services and containers
- **OpenSSH**: Secure remote connection setup
- **Ansible**: Infrastructure as Code tool for handling configuration management and provisioning
- **Docker**: Containerizes applications
- **Traefik**: Serves as our reverse proxy
- **GitHub Actions**: Provides CI/CD pipeline for automated build and deployments
- **Monitoring Stack**:
  - **Prometheus** - Time-series database and monitoring solution
  - **Grafana** - Visualization and dashboarding platform for metrics from prometheus
  - **Alertmanager** - Handles alerts from Prometheus and routes notifications
  - **Node exporter** - Exports host-level(VM) metrics for Prometheus consumption
  - **cAdvisor** - Provides container resource usage and performance metrics

## ⚙️ Project Setup and Workflow

### 1. Infrastructure Setup

- Created an EC2 instance on AWS with OpenSSH installed
- Configured SSH access via local machine's `~/.ssh/config` for secure and easy login
  ```
   Host vps
     HostName <EC2_PUBLIC_IP>
     User ubuntu
     IdentityFile ~/.ssh/vps-key.pem
  ```

### 2. Provisioning with Ansible

Ansible roles were used to automate and modularize the provisioning process:

| Role         | Description                                                                                                              |
| ------------ | ------------------------------------------------------------------------------------------------------------------------ |
| `hostname`   | Sets a custom hostname (vps) on the VM                                                                                   |
| `essentials` | Installs base packages, enables services on boot, and sets up a bridge network                                           |
| `security`   | Applies security configurations including `nftables` rules and allowed SSH ports                                         |
| `kernel`     | Applies kernel tweaks (e.g., enables IP forwarding)                                                                      |
| `docker`     | Installs and configures Docker                                                                                           |
| `traefik`    | Installs and sets up Traefik as a reverse proxy for routing to containers                                                |
| `monitoring` | Sets up a monitoring stack including, Grafana, Prometheus and Alertmanager to monitor system and application performance |

Each of these roles was carefully ordered and executed via an Ansible playbook.

### 3. Service Routing with Traefik

Ansible roles were used to automate and modularize the provisioning process:

- Traefik was configured to route traffic dynamically to Docker containers based on labels.
- It also handled HTTPS and domain-based routing

### 4. Monitoring Stack

- Node Exporter provided system-level metrics, while cAdvisor served container-level metrics.
- Prometheus scraped metrics from Docker containers and exporters.
- Grafana visualized metrics from Prometheus.
- Alertmanager sent notifications for threshold breaches (like high CPU/RAM usage).

### 5. CI/CD with GitHub Actions

For another project ([Task Tracker Repo](https://github.com/wynn-stan/task-tracker)), I set up a simple CI/CD pipeline that handles build and deployment using Docker and GitHub Actions.

#### Primary artificats

- **Dockerfile**: Set's up a multi-stage build resulting in a minimized the final image (~230MB), improving performance and deploy time.
- **docker-compose.yml**: Defines and runs the Docker container on the EC2 instance with appropriate configuration.
- **deploy.yml (GitHub Actions)**:
  - Trigged on git push
  - Builds a Docker Image
  - Pushes image to DockerHub
  - SSH into EC2, pull the latest image and run the container

### 6. Local DNS Mapping

- Added the following entry to host machine's `/etc/hosts` file to map custom domains to the VM:
  ```
  <EC2_PUBLIC_IP>  grafana.cloud.test
  ```
- Traefik routes requests based on these host rules.

## 🎯 Outcome

1. Successfully automated the provisioning of a secure, production-like server environment.
2. Deployed a containerized application with minimal downtime.
3. Real-time monitoring and alerting provided insight into system health
4. CI/CD pipeline enabled seamless code deployment directly from GitHub.
5. Infrastructure as Code via Ansible promotes repeatability and scalability.

## ✅Further Improvements

- TLS via Let’s Encrypt
- Add automated backups

---

## 📸 Preview

![Github Actions](./media/github-actions.png 'Github Actions')
![Traefik Overview](./media/traefik-overview.png 'Traefik Overview')
![Traefik Details](./media/traefik-details.png 'Traefik Details')
![Task Tracker](./media/grafana.png 'Task Tracker')
![Task Tracker](./media/task-tracker.png 'Task Tracker')
