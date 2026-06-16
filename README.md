```markdown
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

## 📁 Struktur Repositori

```text
linux-fleet-automation/
│
├── README.md                 # Dokumentasi utama proyek
├── inventory/                
│   ├── inventory.ini         # Static inventory Ansible
│   └── aws_ec2.yml           # Dynamic inventory AWS
├── playbooks/
│   ├── site.yml              # Playbook untuk instalasi paket dasar (Nginx, dll)
│   ├── hardening.yml         # Playbook untuk keamanan (SSH, UFW, Fail2ban)
│   └── backup.yml            # (Opsional) Playbook untuk manajemen backup
├── scripts/
│   ├── audit.sh              # Script untuk mengecek status dan resource server
│   └── backup.sh             # Script untuk backup direktori /etc
├── docs/
│   ├── security-audit-report.md # Laporan hasil hardening
│   ├── backup-report.md         # Laporan konfigurasi backup
│   └── topology.png             # Gambar topologi jaringan
└── ansible.cfg               # Konfigurasi kustom Ansible

```

---

## 🚀 Panduan Langkah demi Langkah

### Langkah 1: Persiapan Infrastruktur (AWS EC2)

Pastikan Anda telah membuat 3 buah *instance* EC2 di AWS dengan spesifikasi berikut:

* **OS:** Ubuntu 24.04
* **Instance Type:** `t3.micro`
* **Security Group:** Izinkan *Inbound* untuk port `22/tcp` (SSH), `80/tcp` (HTTP), dan `443/tcp` (HTTPS).
* **Tag AWS:** Berikan tag `Role = webserver` pada ketiga *instance* (dibutuhkan untuk *Dynamic Inventory*).

### Langkah 2: Konfigurasi Inventory (Static & Dynamic)

**1. Static Inventory (`inventory/inventory.ini`)**
File ini memetakan IP publik server Anda dan mendefinisikan kunci SSH untuk akses jarak jauh. Sesuaikan IP dan letak file `.pem` Anda.

```ini
[webservers]
server-web1 ansible_host=108.136.40.150 ansible_user=ubuntu ansible_ssh_private_key_file=/path/to/server-web1.pem
server-web2 ansible_host=108.136.249.240 ansible_user=ubuntu ansible_ssh_private_key_file=/path/to/server-web2.pem
server-web3 ansible_host=108.136.44.234 ansible_user=ubuntu ansible_ssh_private_key_file=/path/to/server-web3.pem

```

*Test koneksi dasar:*

```bash
ansible all -i inventory/inventory.ini -m ping

```

**2. Dynamic Inventory AWS (`inventory/ec2.yml`)**
Untuk lingkungan dinamis yang terus berkembang, kita menggunakan *plugin* EC2 Ansible yang akan secara otomatis mendeteksi *instance* berdasarkan tag `Role=webserver`.
*Test Dynamic Inventory:*

```bash
ansible-inventory -i inventory/ec2.yml --graph

```

### Langkah 3: Base Setup & Provisioning (`playbooks/site.yml`)

Playbook ini bertugas melakukan pembaruan sistem dan menginstal paket-paket penting seperti `git`, `htop`, `nginx`, `fail2ban`, dan `ufw`.

* **Eksekusi Playbook:**
```bash
ansible-playbook -i inventory/inventory.ini playbooks/site.yml

```


* **Verifikasi Nginx:**
```bash
ansible all -i inventory/inventory.ini -m shell -a "systemctl status nginx --no-pager"

```



### Langkah 4: Security Hardening (`playbooks/hardening.yml`)

Tahap kritis untuk mengamankan server dari serangan luar. Playbook ini akan:

1. Mematikan fitur Login Root (`PermitRootLogin no`).
2. Mematikan Login Password, memaksa penggunaan SSH Key (`PasswordAuthentication no`).
3. Mengaktifkan *Firewall* (UFW) hanya untuk port 22, 80, dan 443.
4. Mengaktifkan `fail2ban` untuk memblokir IP yang melakukan percobaan login SSH berulang kali secara gagal.

* **Eksekusi Playbook:**
```bash
ansible-playbook -i inventory/inventory.ini playbooks/hardening.yml

```


* **Verifikasi Hardening:**
```bash
ansible all -i inventory/inventory.ini -b -m shell -a "ufw status"
ansible all -i inventory/inventory.ini -m shell -a "grep PermitRootLogin /etc/ssh/sshd_config"

```



### Langkah 5: Audit & Backup (Shell Scripts)

Di dalam direktori `scripts/`, terdapat *script* Bash untuk membantu operasional sehari-hari.

**1. System Audit (`scripts/audit.sh`)**
Mengumpulkan informasi *hostname*, versi OS, *uptime*, port yang terbuka, status *firewall*, dan kapasitas *disk*.

```bash
chmod +x scripts/audit.sh
./scripts/audit.sh

```

**2. Automated Backup (`scripts/backup.sh`)**
Mengompres direktori konfigurasi penting (`/etc`) menjadi file `.tar.gz` yang diberi stempel waktu (*timestamp*).

```bash
chmod +x scripts/backup.sh
sudo ./scripts/backup.sh

```

**Konfigurasi Cron (Automasi Jadwal)**
Untuk menjalankan *backup* secara otomatis setiap hari pukul 01:00 AM, tambahkan *job* berikut ke dalam `crontab`:

```bash
crontab -e
# Tambahkan baris di bawah ini:
0 1 * * * /opt/scripts/backup.sh

```

---

## 📑 Dokumentasi & Laporan Pelaksanaan

Setelah setup selesai, semua hasil konfigurasi dicatat di dalam direktori `docs/`:

* [Security Hardening Report](https://www.google.com/search?q=docs/hardening-report.md) - Detail kebijakan keamanan yang diterapkan.
* [Backup Report](https://www.google.com/search?q=docs/backup-report.md) - Detail siklus dan lokasi direktori penyimpanan *backup*.
