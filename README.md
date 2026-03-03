# Personal Cloud Infrastructure ☁️🐳

This repository contains the Infrastructure as Code (IaC) configuration for my personal cloud server. It relies on Docker and Docker Compose to maintain a modular, reproducible, and secure containerized environment. 

Traffic routing and SSL termination are automatically handled by **Traefik**, while **Portainer** provides a visual management interface.

## 🏗️ Architecture & Tech Stack

* **Hosting Provider:** Hetzner Cloud (Ubuntu 24.04 LTS)
* **Container Engine:** Docker & Docker Compose
* **Reverse Proxy:** Traefik
* **Container Management:** Portainer CE
* **DNS Management:** Cloudflare (DNS-only mode for Let's Encrypt HTTP challenge)

## 🔒 Security
Before deploying any containers, the underlying VPS was secured using best practices:
* Non-root user with `sudo` privileges created.
* SSH Hardening: Root login disabled, password authentication disabled (Public Key/SSH Key only).
* **Fail2Ban** installed and configured to monitor the custom SSH port via `systemd` journal.
* **Hetzner Cloud Firewall** implemented to restrict inbound traffic to ports `80`, `443`, and the custom SSH port (avoiding `ufw` conflicts with Docker's `iptables` rules).
