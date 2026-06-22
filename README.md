# 🚀 Personal Home Server & NAS Setup

A lightweight, robust, and highly efficient Personal Home Server and NAS (Network Attached Storage) ecosystem built on top of enterprise-grade virtualization, containerized microservices, and dedicated hardware passthrough.

This project outlines the architecture, configuration, and orchestration layers used to deploy a high-performance local cloud, media streaming platform, and dev environment.

---

## 🛠️ Tech Stack & Architecture

The infrastructure is built on a layered architecture separating bare-metal virtualization, local cloud dashboard, and container management:

| Layer | Technology | Description |
| :--- | :--- | :--- |
| **Hypervisor** | **Proxmox VE (PVE)** | Type-1 Bare-Metal Hypervisor managing hardware allocations and LXC instances. |
| **OS Environment** | **Linux Containers (LXC)** | Unprivileged Debian architecture for lightweight isolation and minimal overhead. |
| **Cloud Dashboard** | **CasaOS** | Web-based cloud management UI for easy accessibility and storage insights. |
| **Orchestration** | **Docker & Portainer CE** | Container engine paired with Portainer for advanced microservice administration and stack deployments. |
| **Media Server** | **Jellyfin** | Self-hosted, open-source media streaming ecosystem with dedicated hardware parsing. |

---

## 💾 Storage Topology

Reliable storage allocation is crucial for a NAS deployment. This setup utilizes a dedicated hardware mount architecture:

* **Primary Boot & System Disk:** Local LVM volume for microservice runtimes and operating system operations.
* **Data Storage Array (`TOSHIBA-1TB`):** A dedicated 1TB storage drive attached via an optimized Proxmox mount point (`mp0`) directly mapped to `/mnt/toshiba-hdd` inside the container.
* **Permission Layer:** Enforced using explicit user-mapping and access control lists (`chmod/chown`) to allow seamless, cross-container data read/write states for Docker runtimes.

---

## 🐳 Containerized Microservices

Managed dynamically via **Portainer CE**, the stack leverages specific network policies and volume mappings to provide decoupled services:

### 📺 Jellyfin Media Server
* **Volume Mounts:** Mapped directly to host storage paths (`/mnt/toshiba-hdd/Film -> /movies`) for instant media library indexing.
* **Network:** Operating on local port mapping for seamless network broadcasting and media streaming.

### ☸️ Portainer Community Edition
* **Runtime:** Connected directly to the local `/var/run/docker.sock` daemon.
* **Purpose:** Allows advanced deployment of custom multi-container applications via Docker Compose (Stacks) such as download boxes, self-hosted automation engines, and system monitors.

---

## ⚙️ Core Configuration & Deployment Insights

### Proxmox LXC Mount Point Generation
To safely mount external storage into an unprivileged LXC without risking kernel panics or configuration drift, a secure Proxmox Resource Mapping was implemented:

```text
Mount Point ID: 0 (mp0)
Storage Node: TOSHIBA-1TB
Target Container Path: /mnt/toshiba-hdd
