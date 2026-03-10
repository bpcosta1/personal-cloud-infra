# ☁️ Personal Cloud Infrastructure & CI/CD Environment

**Objective:** Set up a secure and resilient production environment on a Hetzner VPS to host some of my web projects and self-host some daily tools I use.
This repository documents the architecture of this ecosystem. 

## 💻 Hosted Services & their purpose

This server hosts a curated stack of self-hosted applications and custom projects:

* **Syncthing:** A peer-to-peer file synchronization tool used to sync my Obsidian vault across my personal devices.
* **WireGuard / wg-easy:** Acts as my personal VPN server to secure internet traffic on untrusted public Wi-Fi networks. This is supplemented by a custom NetworkManager Dispatcher script on my Arch Linux laptop and the WG Tunnel app on my phone, both configured to auto-toggle the VPN connection based on recognized trusted SSIDs.
* **Vaultwarden:** A self-hosted password manager that acts as a lightweight alternative to the official Bitwarden server. It is fully compatible with all standard Bitwarden mobile apps and browser extensions, allowing me to seamlessly sync my passwords, secure notes, and 2FA tokens.
* **Web Apps:** Hosts my web projects securely behind Cloudflare and updated automatically via a GitHub Actions CI/CD pipeline.
* **Portainer:** Provides a graphical interface to quickly inspect container logs, networks, and volumes without needing to SSH into the machine.

## 🏗️ Architecture & Implementation Phases

### Hardening and Security
* **User Management:** Created a non-root user with sudo privileges and migrated Hetzner's provisioned SSH keys with strict permissions (`700` for `.ssh` directory, `600` for `authorized_keys`).
* **SSH Hardening:** Modified `/etc/ssh/sshd_config` to explicitly disable `PermitRootLogin` and `PasswordAuthentication`.
* **Firewall Configuration:** Configured the Hetzner Cloud Firewall with strict inbound rules limited to TCP 22 (SSH), TCP 80 (HTTP), and TCP 443 (HTTPS).
* **Intrusion Prevention:** Installed and configured Fail2Ban to protect the SSH port against brute-force attacks.

### Container Engine
* **Docker Setup:** Installed Docker Engine and Docker Compose and added the non-root user to the `docker` group.
* **Networking:** Created an external Docker network named `proxy-network` to handle all incoming reverse proxy traffic. Enforced a strict policy of zero exposed ports for backend applications within all Docker Compose files.

### Traffic and Visual Management
* **Reverse Proxy (Traefik):** Deployed Traefik connected to the `proxy-network` with global HTTP to HTTPS redirection.
* **SSL & DNS Integration:** Integrated Traefik with the Cloudflare API utilizing the Let's Encrypt DNS-01 challenge. This generates SSL certificates invisibly while keeping Cloudflare's "Orange Cloud" (Proxied Mode) active for DDoS mitigation.

### Applications and CI/CD
* **GitHub Actions:** Created `.github/workflows/deploy.yml` pipelines for the web applications to build multi-stage Docker images and push them to the GitHub Container Registry (GHCR).
* **Automated Deployment:** Configured the pipeline to SSH into the VPS, pull the latest GHCR images, and run `docker compose up -d` for zero-downtime rolling deployments.

### Production Assurance (Observability & Backups)
* **External Monitoring:** Configured Uptime Robot with HTTP(s) monitors mapped to individual subdomains to track uptime and availability.
* **Internal Observability:** Deployed a monitoring stack using Prometheus, Node Exporter, and Grafana to track server metrics internally.
* **(To-Do) Backups:** Implement automated backups for persistent Docker volumes to an off-site Raspberry Pi NAS.
