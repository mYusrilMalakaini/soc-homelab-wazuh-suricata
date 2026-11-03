# SOC Analyst Home Lab — Progress Log

## 1. Lingkungan Proyek

Proyek ini merupakan implementasi home lab Security Operations Center (SOC) menggunakan Wazuh dan Suricata untuk memantau aktivitas endpoint serta jaringan.

Lingkungan laboratorium dibangun menggunakan Oracle VirtualBox dengan jaringan internal `192.168.77.0/24`.

## 2. Komponen Lab dan Konektivitas

| Komponen | Hostname | IP Address | Keterangan |
|---|---|---|---|
| Wazuh Manager | `wazuh-server` | `192.168.77.10` | Pemantauan terpusat dan pengelolaan alert |
| Ubuntu Server | `jh-server` | `192.168.77.20` | Endpoint target dan sensor Suricata |
| Kali Linux | `kali` | `192.168.77.30` | Mesin pengujian |

**Detail lingkungan:**
- Virtualisasi: Oracle VirtualBox
- Jaringan: Internal Network (`intnet`)
- Subnet: `192.168.77.0/24`
- Wazuh Agent: `4.14.9`
- Suricata: `8.0.4`
- Sistem operasi target: Ubuntu Server `20.04.5 LTS`
- Interface Suricata: `enp0s3`
- Log EVE Suricata: `/var/log/suricata/eve.json`

## 3. Hasil Deteksi yang Telah Divalidasi

### 3.1 Deteksi Kegagalan Autentikasi SSH

Pengujian dilakukan dengan menjalankan percobaan autentikasi SSH yang sengaja gagal dari Kali Linux menuju Ubuntu Server.

Wazuh berhasil menerima event autentikasi dan menghasilkan alert berikut:

| Rule ID | Level | Deskripsi |
|---|---:|---|
| `2502` | 10 | PAM 2 more authentication failures |
| `5710` | 5 | Percobaan login menggunakan pengguna yang tidak ditemukan |
| `5503` | 5 | Kegagalan login PAM |

MITRE ATT&CK yang terasosiasi: `T1110` — Brute Force.

**Bukti pengujian:**

![Wazuh SSH brute force alert](../screenshots/wazuh/05-wazuh-ssh-brute-force.png)

![Wazuh SSH authentication failure](../screenshots/wazuh/06-wazuh-ssh-auth-failure.png)

![Simulasi SSH failed login dari Kali Linux](../screenshots/testing/07-kali-ssh-failed-login.png)

![Ringkasan autentikasi pada dashboard Wazuh](../screenshots/wazuh/08-wazuh-authentication-overview.png)

### 3.2 File Integrity Monitoring (FIM)

Pengujian File Integrity Monitoring dilakukan dengan membuat dan memodifikasi file pada endpoint Ubuntu Server.

Wazuh berhasil mendeteksi perubahan integritas file melalui alert berikut:

| Rule ID | Keterangan |
|---|---|
| `554` | File ditambahkan |
| `550` | Checksum file berubah |

**Bukti pengujian:**

![FIM file added - Rule 554](../screenshots/wazuh/03-fim-file-added-554.png)

![FIM file modified - Rule 550](../screenshots/wazuh/04-fim-file-modified-550.png)

### 3.3 Network Intrusion Detection — TCP SYN Scan

Suricata dikonfigurasi untuk memantau trafik jaringan pada interface `enp0s3`. Wazuh Agent pada Ubuntu Server memantau file log Suricata `eve.json`.

Pengujian dilakukan dengan menjalankan Nmap SYN scan dari Kali Linux menuju Ubuntu Server:

```bash
nmap -sS -T3 --top-ports 100 192.168.77.20
```

Custom rule Suricata digunakan untuk mendeteksi pola TCP SYN scan dari alamat IP Kali Linux.

| Komponen | Hasil |
|---|---|
| Suricata Signature ID | `1000001` |
| Signature | `LOCAL DETECT Kali Linux TCP SYN Scan` |
| Source IP | `192.168.77.30` |
| Destination IP | `192.168.77.20` |
| Wazuh Rule ID | `86601` |
| Wazuh Level | 3 |

Alert berhasil diterima oleh Wazuh Manager melalui integrasi log Suricata.

**Bukti pengujian:**

![Wazuh Suricata SYN scan alert](../screenshots/suricata/10-wazuh-suricata-syn-scan-alert.png)

Pengujian ini merupakan simulasi pemindaian port terkontrol. Alert menunjukkan aktivitas terdeteksi, bukan berarti trafik berhasil diblokir.

### 3.4 IOC / Suspicious File Hash Detection

Pengujian dilakukan dengan membuat file simulasi pada Ubuntu Server dan mencocokkan SHA-256 hash file tersebut dengan daftar IOC lokal.

Script `check-ioc.sh` digunakan untuk menghitung hash dan membandingkannya dengan daftar hash pada `ioc-hashes.txt`.

Hasil pengujian terdiri dari dua kondisi:

- **Positive match:** hash file cocok dengan daftar IOC dan menghasilkan pesan `IOC_MATCH`.
- **Negative match:** hash file tidak cocok dan menghasilkan pesan `NO_MATCH`.

Wazuh Agent memantau log `/var/log/ioc-lab/hash-detection.log`. Custom rule pada Wazuh Manager menggunakan konfigurasi berikut:

| Rule ID | Level | Deskripsi |
|---|---:|---|
| `100320` | 10 | IOC Detection: File SHA256 matches local IOC list |

Alert berhasil diterima oleh Wazuh Manager dengan informasi agent `jh-server`, alamat IP `192.168.77.20`, serta hash file yang cocok dengan daftar IOC.

**Bukti pengujian:**

![Daftar IOC hash](../screenshots/case-04-ioc-hash-detection/11-ioc-hash-list.png)

![Validasi script IOC hash](../screenshots/case-04-ioc-hash-detection/12-ioc-script-validation.png)

![Wazuh IOC detection alert](../screenshots/case-04-ioc-hash-detection/13-wazuh-ioc-alert.png)

![Detail Wazuh IOC alert](../screenshots/case-04-ioc-hash-detection/14-wazuh-ioc-alert-details.png)

![Validasi custom rule IOC pada Wazuh](../screenshots/case-04-ioc-hash-detection/15-wazuh-ioc-rule-validation.png)

**Catatan:** hash yang digunakan merupakan IOC simulasi yang dibuat sendiri untuk keperluan pengujian. Hash tersebut bukan indikator malware yang telah diverifikasi dan file pengujian bukan malware.

### 3.5 Suspicious Network Activity — Koneksi TCP ke Port 4444

Pengujian dilakukan dari Kali Linux (`192.168.77.30`) menuju Ubuntu Server (`192.168.77.20`) pada port TCP `4444`. Suricata menggunakan rule lokal dengan Signature ID `1000002` untuk mengamati paket TCP SYN menuju port tersebut.

Event alert teramati pada log EVE Suricata dengan rincian:

| Atribut | Hasil |
|---|---|
| Suricata Signature ID | `1000002` |
| Signature | `LOCAL DETECT Repeated TCP Connection Attempts to Port 4444` |
| Source IP | `192.168.77.30` |
| Destination IP | `192.168.77.20` |
| Destination port | `4444` |
| Protocol | `TCP` |
| Direction | `to_server` |
| Action | `allowed` |
| Severity | `2` |

Event yang sama terlihat pada dashboard Wazuh melalui rule berikut:

| Atribut | Hasil |
|---|---|
| Wazuh Rule ID | `86601` |
| Wazuh Level | `3` |
| Agent | `jh-server` |
| Agent ID | `001` |
| Suricata Signature ID | `1000002` |

**Bukti pengujian:**

![Wazuh suspicious network events](../screenshots/case-05-suspicious-network-activity/16-wazuh-suspicious-network-events.png)

![Wazuh suspicious network alert details](../screenshots/case-05-suspicious-network-activity/17-wazuh-suspicious-network-details.png)

![Suricata suspicious network alert](../screenshots/case-05-suspicious-network-activity/18-suricata-suspicious-network-alert.png)

**Batasan validasi:** rule dikonfigurasi dengan threshold `count 5, seconds 10`, tetapi event yang diamati muncul pada waktu yang terpisah sekitar 69 detik. Karena itu, pemenuhan threshold lima koneksi dalam sepuluh detik belum dapat dinyatakan tervalidasi. Nilai `action: allowed` juga menunjukkan bahwa trafik terdeteksi tetapi tidak diblokir oleh rule alert tersebut.

Dokumentasi lengkap tersedia pada [Suspicious Network Activity Detection](detections/suspicious-network-activity.md).

## 4. Deteksi Malware — Dalam Proses

Pengujian malware detection menggunakan ClamAV pada Ubuntu Server masih dalam proses.

Komponen yang telah disiapkan:

- ClamAV telah dipasang pada Ubuntu Server.
- File uji EICAR telah dibuat sebagai test file antivirus yang tidak berbahaya.
- Wazuh Agent telah dikonfigurasi untuk memantau `/var/log/clamav/eicar-scan.log`.
- Custom Wazuh rule `100310` telah dibuat untuk mengenali event pengujian ClamAV.
- Konfigurasi rule telah divalidasi menggunakan `wazuh-analysisd -t` dan `wazuh-logtest`.
- Event uji manual berhasil menghasilkan alert Wazuh Rule ID `100310`, Level 10.

**Kendala:**

Database signature ClamAV belum berhasil diperbarui karena pembatasan akses dan cooldown dari server pembaruan database.

**Status validasi:**

Integrasi event log manual dengan Wazuh telah berhasil diuji. Namun, deteksi hasil pemindaian EICAR secara langsung oleh ClamAV belum tervalidasi. Oleh karena itu, skenario malware detection masih berstatus *Dalam Proses*.

## 5. Pekerjaan yang Masih Berjalan

1. Menyelesaikan pembaruan database ClamAV dan memvalidasi pemindaian EICAR secara langsung.
2. Memvalidasi threshold rule Suspicious Network Activity melalui pengujian terukur untuk memastikan lima koneksi terdeteksi dalam sepuluh detik.
3. Menambahkan atribusi path file yang lebih spesifik pada hasil pengujian FIM.
4. Mengeksplorasi konfigurasi FIM Who-data.
5. Meninjau peringatan terkait audit event dispatch.
6. Mengimplementasikan dan menguji Wazuh Active Response.
7. Melengkapi dokumentasi investigasi insiden berdasarkan alert yang telah dihasilkan.

## 6. Catatan Keamanan

- Seluruh pengujian dilakukan dalam lingkungan VirtualBox terisolasi menggunakan jaringan internal.
- Percobaan login SSH gagal, pemindaian Nmap, dan koneksi TCP ke port 4444 merupakan aktivitas simulasi terkontrol.
- Pengujian IOC menggunakan hash buatan sendiri, bukan hash malware yang telah diverifikasi.
- File EICAR merupakan file uji antivirus standar dan bukan malware aktif.
- Alert yang dihasilkan menunjukkan aktivitas terdeteksi oleh sistem monitoring. Alert tidak otomatis berarti aktivitas berhasil diblokir.
- Hasil pengujian hanya dinyatakan tervalidasi sesuai ruang lingkup bukti yang benar-benar diamati.

## 7. Ringkasan

Implementasi SOC home lab telah berhasil memvalidasi beberapa skenario deteksi menggunakan Wazuh dan Suricata, yaitu:

- SSH Authentication Failure Detection.
- File Integrity Monitoring.
- Network Intrusion Detection melalui TCP SYN Scan.
- IOC / Suspicious File Hash Detection.
- Deteksi event koneksi TCP ke port 4444 melalui Suricata dan Wazuh.

Integrasi Suricata dan Wazuh telah menghasilkan alert dari aktivitas jaringan yang disimulasikan. Wazuh juga berhasil menerima event autentikasi, perubahan integritas file, dan pencocokan hash IOC.

Validasi threshold rule Suspicious Network Activity masih perlu dilanjutkan. Skenario Malware Detection menggunakan ClamAV juga masih dalam proses karena pembaruan database signature belum berhasil diselesaikan.

Pengembangan selanjutnya difokuskan pada validasi lanjutan tersebut, pengujian ClamAV secara langsung, dan implementasi Active Response.
