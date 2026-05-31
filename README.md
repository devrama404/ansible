# 🚀 Ansible DevOps Automation

> Infrastructure as Code (IaC) untuk mengotomatisasi provisioning dan konfigurasi lingkungan DevOps menggunakan Ansible.

Project ini menyediakan playbook dan role modular untuk melakukan instalasi berbagai komponen DevOps seperti Docker, Kubernetes, Nginx, Prometheus, Grafana, PostgreSQL, MariaDB, Redis, dan Terraform secara otomatis dan konsisten.

---

## 📋 Overview

Mengelola banyak server secara manual sering kali memakan waktu, rentan kesalahan konfigurasi, dan sulit direproduksi.

Repository ini dibuat untuk membantu:

| Manfaat | Deskripsi |
|---------|-----------|
| ⚡ Mempercepat Provisioning | Setup server baru dalam hitungan menit, bukan jam |
| 🎯 Mengurangi Human Error | Konfigurasi terstandarisasi melalui code |
| 🔁 Konsistensi Environment | Dev, Staging, Production identik |
| 📜 Infrastructure as Code | Version control untuk infrastruktur |
| 🔄 Mudah Direplikasi | Clone environment kapan saja |

---

## ✨ Features

### 🐳 Container Platform
- [x] Docker Engine (latest stable)
- [x] Docker Compose Plugin

### ☸️ Kubernetes
- [x] kubeadm (cluster initialization)
- [x] kubelet (node agent)
- [x] kubectl (CLI management)

### 🌐 Web & Reverse Proxy
- [x] Nginx (web server & reverse proxy)
- [x] Auto SSL ready (Certbot compatible)

### 📊 Monitoring & Observability
- [x] Prometheus (metrics collection)
- [x] Grafana (visualization dashboard)
- [x] Node Exporter (system metrics)

### 🗄️ Databases
- [x] PostgreSQL (relational database)
- [x] MariaDB (MySQL compatible)
- [x] Redis (in-memory cache)

### 🛠️ Utilities
```bash
git curl wget jq vim htop tree net-tools python3 python3-pip
```

### 🏗️ Infrastructure as Code
- [x] Terraform CLI (multi-cloud provisioning)

---

## 🏗 Architecture

```text
                    ┌─────────────────────┐
                    │   Ansible Control   │
                    │      Node           │
                    │  (Your Laptop/CI)   │
                    └─────────┬───────────┘
                              │ SSH (22)
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         ▼                    ▼                    ▼

 ┌────────────────┐ ┌────────────────┐ ┌────────────────┐
 │  DevOps Node   │ │   K8S Node     │ │  Monitoring    │
 ├────────────────┤ ├────────────────┤ ├────────────────┤
 │ • Docker       │ │ • kubeadm      │ │ • Prometheus   │
 │ • Docker Compose││ • kubelet      │ │ • Grafana      │
 │ • Nginx        │ │ • kubectl      │ │ • Node Exporter│
 │ • PostgreSQL   │ │ • CNI Plugin   │ │ • Alertmanager │
 │ • Redis        │ │                │ │                │
 └────────────────┘ └────────────────┘ └────────────────┘
```

---

## 📁 Project Structure

```text
ansible-devops/
├── 📄 inventory.example.ini      # Template inventory server
├── 📄 site.yml                   # Main playbook (entry point)
├── 📄 README.md                  # Dokumentasi ini
├── 📄 .gitignore                 # Ignore files untuk Git
├── 📄 ansible.cfg                # Konfigurasi Ansible (opsional)
│
├── 📂 group_vars/
│   └── 📄 all.yml                # Variabel global untuk semua host
│
├── 📂 roles/
│   ├── 📂 common/                # Setup dasar server
│   │   └── 📂 tasks/
│   │       └── 📄 main.yml
│   │
│   ├── 📂 docker/                # Install Docker & Compose
│   │   └── 📂 tasks/
│   │       └── 📄 main.yml
│   │
│   ├── 📂 kubernetes/            # Setup Kubernetes cluster
│   │   └── 📂 tasks/
│   │       └── 📄 main.yml
│   │
│   ├── 📂 nginx/                 # Konfigurasi Nginx
│   │   └── 📂 tasks/
│   │       └── 📄 main.yml
│   │
│   ├── 📂 monitoring/            # Prometheus + Grafana stack
│   │   └── 📂 tasks/
│   │       └── 📄 main.yml
│   │
│   ├── 📂 databases/             # PostgreSQL, MariaDB, Redis
│   │   └── 📂 tasks/
│   │       └── 📄 main.yml
│   │
│   └── 📂 terraform/             # Install Terraform CLI
│       └── 📂 tasks/
│           └── 📄 main.yml
│
└── 📂 files/                     # Static files untuk copy ke server
    └── 📄 ...
```

---

## ⚙️ Requirements

### 🔹 Control Node (Mesin yang menjalankan Ansible)
| Komponen | Versi Minimum | Cara Install |
|----------|--------------|--------------|
| Ansible | 2.15+ | `pip install ansible` |
| Python | 3.10+ | `sudo apt install python3` |
| SSH Client | - | `sudo apt install openssh-client` |

### 🔹 Managed Nodes (Server Target)
| OS | Versi | Catatan |
|----|-------|---------|
| Ubuntu | 22.04 LTS / 24.04 LTS | ✅ Direkomendasikan |
| Debian | 12 (Bookworm) | ✅ Support penuh |
| RHEL/CentOS | 8/9 | ⚠️ Perlu penyesuaian minor |

### 🔹 Persiapan Server Target
```bash
# Pastikan server memiliki:
- SSH Server aktif (port 22)
- User dengan sudo privileges
- Koneksi internet untuk download package
- Minimal 2GB RAM (untuk Kubernetes)
```

---

## 🚀 Quick Start

### 1️⃣ Clone Repository
```bash
git clone https://github.com/yourusername/ansible-devops.git
cd ansible-devops
```

### 2️⃣ Konfigurasi Inventory
Salin template inventory dan edit sesuai server Anda:
```bash
cp inventory.example.ini inventory.ini
nano inventory.ini
```

Contoh `inventory.ini`:
```ini
# Single Server
[devops]
203.0.113.10 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

# Multiple Servers (untuk Kubernetes cluster)
[k8s_masters]
203.0.113.10 ansible_user=ubuntu

[k8s_workers]
203.0.113.11 ansible_user=ubuntu
203.0.113.12 ansible_user=ubuntu

[monitoring]
203.0.113.20 ansible_user=ubuntu

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

### 3️⃣ Verifikasi Koneksi
```bash
# Test koneksi ke semua server
ansible all -i inventory.ini -m ping

# Expected output:
# 203.0.113.10 | SUCCESS => { "changed": false, "ping": "pong" }
```

### 4️⃣ Jalankan Deployment
```bash
# Deploy semua komponen
ansible-playbook -i inventory.ini site.yml

# Deploy komponen tertentu saja (contoh: hanya Docker)
ansible-playbook -i inventory.ini site.yml --tags docker

# Deploy dengan verbose output (debug)
ansible-playbook -i inventory.ini site.yml -vvv
```

### 5️⃣ Verifikasi Hasil
```bash
# Cek Docker
ssh ubuntu@203.0.113.10 "docker --version"

# Cek Kubernetes
ssh ubuntu@203.0.113.10 "kubectl cluster-info"

# Cek Nginx
curl http://203.0.113.10

# Akses Grafana
# Buka browser: http://203.0.113.20:3000
# Default login: admin / admin
```

---

## 📦 Installed Components Reference

| Kategori | Komponen | Port Default | Status |
|----------|----------|--------------|--------|
| 🐳 Container | Docker Engine | - | ✅ Active |
| 🐳 Container | Docker Compose | - | ✅ Active |
| ☸️ Kubernetes | kubeadm/kubelet/kubectl | 6443, 10250 | ⚙️ Ready |
| 🌐 Web Server | Nginx | 80, 443 | ✅ Active |
| 📊 Monitoring | Prometheus | 9090 | ✅ Active |
| 📊 Monitoring | Grafana | 3000 | ✅ Active |
| 📊 Monitoring | Node Exporter | 9100 | ✅ Active |
| 🗄️ Database | PostgreSQL | 5432 | ⚙️ Optional |
| 🗄️ Database | MariaDB | 3306 | ⚙️ Optional |
| 🗄️ Database | Redis | 6379 | ⚙️ Optional |
| 🏗️ IaC | Terraform | - | ✅ Installed |

---

## 🔧 Advanced Usage

### 🎯 Run Specific Role
```bash
# Hanya install Docker
ansible-playbook -i inventory.ini site.yml --tags docker

# Hanya setup monitoring
ansible-playbook -i inventory.ini site.yml --tags monitoring

# Skip database installation
ansible-playbook -i inventory.ini site.yml --skip-tags databases
```

### 🔐 Menggunakan Ansible Vault untuk Secrets
```bash
# Encrypt file yang berisi password/database credentials
ansible-vault encrypt group_vars/all.yml

# Edit file yang di-encrypt
ansible-vault edit group_vars/all.yml

# Run playbook dengan vault password
ansible-playbook -i inventory.ini site.yml --ask-vault-pass

# Atau gunakan file password
ansible-playbook -i inventory.ini site.yml --vault-password-file ~/.vault_pass
```

### 🔄 Dry Run (Check Mode)
```bash
# Lihat apa yang akan dilakukan Ansible tanpa eksekusi
ansible-playbook -i inventory.ini site.yml --check --diff
```

### 🧹 Cleanup/Uninstall
```bash
# Gunakan playbook cleanup jika tersedia
ansible-playbook -i inventory.ini cleanup.yml --ask-become-pass
```

---

## 🔐 Security Best Practices

### Untuk Environment Production:
```yaml
# group_vars/all.yml - Contoh konfigurasi aman
security:
  ssh:
    disable_root_login: true
    password_authentication: false
    allow_users:
      - ubuntu
      - deploy
  
  firewall:
    enable_ufw: true
    allowed_ports:
      - 22    # SSH
      - 80    # HTTP
      - 443   # HTTPS
      - 9090  # Prometheus (internal)
  
  fail2ban:
    enabled: true
    max_retries: 5
    ban_time: 3600
```

### Checklist Keamanan:
- [ ] Gunakan SSH Key Authentication, nonaktifkan password login
- [ ] Aktifkan UFW/iptables firewall dengan whitelist port
- [ ] Install dan konfigurasi Fail2Ban untuk proteksi brute-force
- [ ] Simpan credentials sensitif menggunakan Ansible Vault
- [ ] Batasi akses database hanya dari IP internal/trusted
- [ ] Update sistem secara berkala: `ansible all -m apt -a "upgrade=dist"`

---

## 🐛 Troubleshooting

| Masalah | Solusi |
|---------|--------|
| ❌ `Permission denied` saat SSH | Pastikan key file: `chmod 600 ~/.ssh/id_rsa` |
| ❌ `ansible: command not found` | Install: `pip install ansible` atau `sudo apt install ansible` |
| ❌ `Failed to connect to host` | Cek: IP, user, SSH key, dan firewall server target |
| ❌ `sudo: a command is required` | Pastikan user di server punya sudo privileges |
| ❌ Playbook gagal di tengah jalan | Gunakan `--start-at-task "nama task"` untuk resume |

### Debug Mode
```bash
# Lihat detail eksekusi per task
ansible-playbook -i inventory.ini site.yml -vvv

# Cek fakta server target
ansible all -i inventory.ini -m setup | less
```

---

## 🤝 Contributing

1. Fork repository ini
2. Buat feature branch: `git checkout -b feature/nama-fitur`
3. Commit perubahan: `git commit -am 'feat: tambah support Rocky Linux'`
4. Push ke branch: `git push origin feature/nama-fitur`
5. Buat Pull Request

### Conventional Commit Format:
```
feat:     ✨ Fitur baru
fix:      🐛 Perbaikan bug
docs:     📝 Update dokumentasi
style:    💄 Formatting, semi-colon, dll
refactor: ♻️ Refactor kode (bukan fitur/bugfix)
test:     ✅ Tambah/update test
chore:    🔧 Maintenance, dependencies, config
```

---

## 📜 License

Project ini dirilis di bawah lisensi **MIT License**. Silakan gunakan, modifikasi, dan distribusikan sesuai kebutuhan.

```
MIT License

Copyright (c) 2024 Your Name

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## ⭐ Support

Jika repository ini membantu pekerjaan atau pembelajaran Anda, jangan lupa untuk memberikan ⭐ **Star** di GitHub!

[![GitHub stars](https://img.shields.io/github/stars/yourusername/ansible-devops?style=for-the-badge)](https://github.com/yourusername/ansible-devops/stargazers)

> Dibuat dengan ❤️ menggunakan Ansible, Python & Open Source Tools

---

**📬 Kontak & Support**
- 🐛 Bug Report: [GitHub Issues](https://github.com/yourusername/ansible-devops/issues)
- 💬 Diskusi: [GitHub Discussions](https://github.com/yourusername/ansible-devops/discussions)
- 📧 Email: your.email@example.com
```

### 💾 Cara Menyimpan File:

1. **Blok & Salin** seluruh kode di dalam kotak kode di atas
2. Buka **VS Code**, **Notepad**, atau teks editor pilihan Anda
3. **Paste** kode tersebut
4. Simpan dengan nama persis: `README.md` (pastikan ekstensi `.md`, bukan `.txt`)
5. Letakkan file di **root folder repository** Ansible Anda

### 🔧 Tips Tambahan:
- Ganti `yourusername` dengan username GitHub Anda di seluruh file
- Sesuaikan `inventory.example.ini` dengan topologi server Anda
- Untuk produksi, aktifkan Ansible Vault untuk mengelola secrets

Jika Anda ingin saya buatkan juga file `inventory.example.ini`, `site.yml`, atau contoh `roles/*/tasks/main.yml`, beri tahu saja! 🚀
