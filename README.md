---

# Linux Fleet Automation & Network Hardening

Proyek ini bertujuan untuk mengotomatisasi penyediaan (*provisioning*), konfigurasi, dan pengamanan (*security hardening*) pada sekumpulan server Linux (Ubuntu 24.04) yang berjalan di AWS EC2. Otomatisasi ini digerakkan oleh **Ansible** untuk manajemen konfigurasi skala besar, serta **Shell Script** untuk kebutuhan audit sistem dan *backup* harian.

## 🏗️ Arsitektur & Topologi

```text
  Laptop / Ansible Controller
              |
        Internet (SSH)
              |
---------------------------------
|               |               |
web1          web2           web3
AWS EC2       AWS EC2        AWS EC2
Ubuntu        Ubuntu         Ubuntu
Public IP     Public IP      Public IP

```

## 📂 Struktur Direktori

Proyek ini menggunakan struktur direktori berbasis *roles* agar rapi dan modular:

```text
.
├── group_vars/
│   └── all.yml             # Variabel global yang berlaku untuk semua host
├── roles/                  # Kumpulan module/task untuk setiap layanan
│   ├── common/tasks        # Konfigurasi dasar dan hardening untuk semua server
│   ├── databases/tasks     # Konfigurasi database
│   ├── docker/tasks        # Instalasi dan setup Docker
│   ├── kubernetes/tasks    # Setup cluster Kubernetes
│   ├── monitoring/tasks    # Setup tools monitoring (Prometheus/Grafana, dll)
│   └── nginx/tasks         # Konfigurasi Nginx web server/reverse proxy
├── .gitignore              # Daftar file yang diabaikan oleh Git
├── README.md               # Dokumentasi proyek (file ini)
├── inventory.ini           # Daftar alamat IP dan pengelompokan server (hosts)
└── site.yml                # Playbook utama yang mengatur urutan eksekusi (entrypoint)

```

## ⚙️ Alur Pengerjaan (Step-by-Step)

Berdasarkan konfigurasi pada `site.yml`, Ansible akan mengeksekusi tugas secara berurutan sesuai dengan kelompok server (*host groups*) sebagai berikut:

1. **Tahap 1: Konfigurasi Dasar (Semua Server)**
* **Target:** `hosts: all`
* **Role:** `common`
* **Deskripsi:** Ansible akan masuk ke seluruh server yang ada di `inventory.ini` untuk melakukan pembaruan sistem dasar, instalasi *package* wajib, dan penerapan keamanan dasar (*security hardening*).


2. **Tahap 2: Setup Lingkungan DevOps**
* **Target:** `hosts: devops`
* **Roles:** `docker`, `nginx`
* **Deskripsi:** Ansible akan menginstal mesin *container* Docker dan mengonfigurasi Nginx (biasanya sebagai *Reverse Proxy* atau *Web Server*) khusus pada server yang masuk ke dalam grup `devops`.


3. **Tahap 3: Setup Cluster Kubernetes**
* **Target:** `hosts: k8s`
* **Role:** `kubernetes`
* **Deskripsi:** Ansible akan menyiapkan komponen dan *tools* yang dibutuhkan untuk menjalankan klaster Kubernetes pada server di dalam grup `k8s`.


4. **Tahap 4: Setup Database & Monitoring**
* **Target:** `hosts: monitoring`
* **Roles:** `monitoring`, `databases`
* **Deskripsi:** Terakhir, Ansible akan menginstal layanan *database* serta alat pemantauan (*monitoring*) pada server yang masuk dalam grup `monitoring`.



## 🚀 Cara Penggunaan

### 1. Prasyarat

* **Ansible** telah terinstal di mesin lokal (*Controller*).
* Memiliki akses SSH (*Private Key*) ke seluruh server AWS EC2 tujuan.

### 2. Persiapan Inventory (`inventory.ini`)

Pastikan Anda telah mendefinisikan IP Publik dari *instances* EC2 ke dalam grup yang sesuai dengan `site.yml`. Contoh:

```ini
[devops]
server_devops ansible_host=<IP_PUBLIC> ansible_user=ubuntu

[k8s]
server_k8s ansible_host=<IP_PUBLIC> ansible_user=ubuntu

[monitoring]
server_monitor ansible_host=<IP_PUBLIC> ansible_user=ubuntu

```

### 3. Menjalankan Playbook

Gunakan perintah berikut di terminal Anda untuk memulai proses otomatisasi secara berurutan:

```bash
ansible-playbook -i inventory.ini site.yml

```
