# 🚀 Ansible DevOps Automation

Infrastructure as Code (IaC) project untuk mengotomatisasi provisioning dan konfigurasi lingkungan DevOps menggunakan Ansible.

Project ini menyediakan playbook dan role modular untuk melakukan instalasi berbagai komponen DevOps seperti Docker, Kubernetes, Nginx, Prometheus, Grafana, PostgreSQL, MariaDB, Redis, dan Terraform secara otomatis dan konsisten.

---

## 📋 Overview

Mengelola banyak server secara manual sering kali memakan waktu, rentan kesalahan konfigurasi, dan sulit direproduksi.

Repository ini dibuat untuk membantu:

* Mempercepat proses provisioning server
* Mengurangi human error
* Menjaga konsistensi konfigurasi
* Mendukung Infrastructure as Code (IaC)
* Mempermudah deployment environment Development, Staging, dan Production

---

## ✨ Features

### Container Platform

* Docker Engine
* Docker Compose

### Kubernetes

* kubeadm
* kubelet
* kubectl

### Web & Reverse Proxy

* Nginx

### Monitoring & Observability

* Prometheus
* Grafana
* Node Exporter

### Databases

* PostgreSQL
* MariaDB
* Redis


### Utilities

* Git
* Curl
* Wget
* jq
* Vim
* HTOP
* Tree
* Net-tools
* Python3
* Pip

---

## 🏗 Architecture

```text
                    ┌─────────────┐
                    │  Ansible    │
                    │ Control Node│
                    └──────┬──────┘
                           │ SSH
         ┌─────────────────┼─────────────────┐
         │                 │                 │
         ▼                 ▼                 ▼

 ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
 │ DevOps Node │  │ K8S Node    │  │ Monitoring  │
 ├─────────────┤  ├─────────────┤  ├─────────────┤
 │ Docker      │  │ kubeadm     │  │ Prometheus  │
 │ Nginx       │  │ kubelet     │  │ Grafana     │
 │             │  │ kubectl     │  │ Exporter    │
 └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Project Structure

```text
ansible-devops/
├── inventory.example.ini
├── site.yml
├── README.md
├── .gitignore
├── group_vars/
│   └── all.yml
├── roles/
│   ├── common/
│   │   └── tasks/
│   │       └── main.yml
│   ├── docker/
│   │   └── tasks/
│   │       └── main.yml
│   ├── kubernetes/
│   │   └── tasks/
│   │       └── main.yml
│   ├── nginx/
│   │   └── tasks/
│   │       └── main.yml
│   ├── monitoring/
│   │   └── tasks/
│   │       └── main.yml
│   └── databases/
│       └── tasks/
│           └── main.yml
```

---

## ⚙️ Requirements

### Control Node

* Ansible 2.15+
* Python 3.10+
* SSH Access

### Managed Nodes

* Ubuntu 22.04 LTS
* Ubuntu 24.04 LTS
* Debian 12
* Sudo Privileges

---

## 🚀 Quick Start

### Clone Repository

```bash
git clone https://github.com/yourusername/ansible-devops.git

cd ansible-devops
```

### Configure Inventory

```ini
[devops]
SERVER_IP ansible_user=ubuntu
```

### Verify Connectivity

```bash
ansible all -i inventory.ini -m ping
```

### Run Deployment

```bash
ansible-playbook -i inventory.ini site.yml
```

---

## 📦 Installed Components

| Category   | Component      |
| ---------- | -------------- |
| Container  | Docker         |
| Container  | Docker Compose |
| Kubernetes | kubeadm        |
| Kubernetes | kubelet        |
| Kubernetes | kubectl        |
| Monitoring | Prometheus     |
| Monitoring | Grafana        |
| Monitoring | Node Exporter  |
| Database   | PostgreSQL     |
| Database   | MariaDB        |
| Database   | Redis          |
| Web Server | Nginx          |
| IaC        | Terraform      |

---

## 🔐 Security Recommendations

Untuk environment production disarankan:

* Menggunakan SSH Key Authentication
* Menonaktifkan login root langsung
* Mengaktifkan UFW Firewall
* Menggunakan Fail2Ban
* Menyimpan secrets menggunakan Ansible Vault
* Membatasi akses database melalui firewall

Contoh penggunaan Ansible Vault:

```bash
ansible-vault encrypt group_vars/all.yml
```


## ⭐ Support

If this repository helps your work, consider giving it a star on GitHub.
