# SOC Analyst Homelab — Wazuh & Suricata

Proyek *cybersecurity home lab* untuk mempelajari operasi SIEM, pemantauan endpoint, deteksi intrusi jaringan, analisis event keamanan, dan investigasi insiden menggunakan Wazuh dan Suricata.

## 1. Gambaran Umum Proyek

Proyek ini menyimulasikan lingkungan Security Operations Center (SOC) sederhana menggunakan beberapa virtual machine di Oracle VirtualBox.

- **Wazuh** digunakan untuk pemantauan keamanan endpoint, pengumpulan log, analisis event, dan pengelolaan alert.
- **Suricata** digunakan untuk memantau lalu lintas jaringan dan menghasilkan alert Network Intrusion Detection System (NIDS).
- **Kali Linux** digunakan untuk melakukan pengujian keamanan terkontrol terhadap server target.

Tujuan utama proyek ini adalah memahami bagaimana event keamanan dihasilkan, dikumpulkan, dideteksi, dan dianalisis dalam lingkungan pengujian yang terkontrol.

## 2. Arsitektur Lab

| Komponen | Teknologi | Peran |
|---|---|---|
| SIEM | Wazuh Manager 4.14.9 | Analisis event dan pengelolaan alert |
| Endpoint Agent | Wazuh Agent 4.14.9 | Pengumpulan log endpoint |
| IDS | Suricata 8.0.4 | Pemantauan lalu lintas jaringan |
| Target | Ubuntu Server 20.04.5 LTS | Endpoint yang dipantau |
| Mesin Pengujian | Kali Linux | Pengujian keamanan terkontrol |
| Virtualisasi | Oracle VirtualBox | Lingkungan virtual lab |

### Topologi Jaringan

| Virtual Machine | IP Internal | Peran |
|---|---|---|
| Wazuh Server | `192.168.77.10` | SIEM |
| Target Server | `192.168.77.20` | Wazuh Agent dan Suricata |
| Kali Linux | `192.168.77.30` | Pengujian keamanan |

- **Jaringan:** `192.168.77.0/24`
- **VirtualBox Network:** Internal Network (`intnet`)

Lihat [Arsitektur Lab](docs/architecture/lab-architecture.md) untuk penjelasan lebih lanjut.

## 3. Skenario Deteksi yang Telah Divalidasi

| Skenario | Deteksi | Hasil |
|---|---|---|
| Custom Wazuh Rule | Rule ID `100300` | Tervalidasi |
| Integrasi Event Suricata | Wazuh Rule ID `86601` | Tervalidasi |
| File Integrity Monitoring | Rule ID `554`, `550` | Tervalidasi |
| Kegagalan Autentikasi SSH | Rule ID `2502`, `5710`, `5503` | Tervalidasi |
| Deteksi TCP SYN Scan | Suricata SID `1000001` | Tervalidasi |
| Deteksi IOC Berdasarkan Hash | Wazuh Rule ID `100320`, Level 10 | Tervalidasi |

Seluruh hasil tersebut berasal dari pengujian terkontrol di lingkungan lab dan bukan merupakan bukti terjadinya serangan nyata.

### Dokumentasi Deteksi

- [Deteksi Kegagalan Autentikasi SSH](docs/detections/ssh-authentication.md)
- [File Integrity Monitoring](docs/detections/file-integrity-monitoring.md)
- [Network Intrusion Detection](docs/detections/network-intrusion.md)
- [Deteksi IOC / Hash File Mencurigakan](docs/detections/ioc-hash-detection.md)

## 4. Bukti Pengujian

Screenshot hasil pengujian dikelompokkan berdasarkan kategori dan skenario:

- [Bukti Wazuh](screenshots/wazuh/)
- [Bukti Suricata](screenshots/suricata/)
- [Bukti Pengujian](screenshots/testing/)
- [Bukti Deteksi IOC Hash](screenshots/case-04-ioc-hash-detection/)

Dokumentasi pada setiap skenario juga memuat gambar bukti pengujian yang relevan.

## 5. Status Proyek

### Telah Divalidasi

- [x] Deployment Wazuh Manager dan Dashboard
- [x] Konektivitas Wazuh Agent
- [x] Pengujian custom Wazuh rule
- [x] Pengumpulan event Suricata oleh Wazuh
- [x] Deteksi pembuatan dan modifikasi file
- [x] Pemantauan kegagalan autentikasi SSH
- [x] Deteksi TCP SYN scan terkontrol
- [x] Deteksi IOC berdasarkan pencocokan hash SHA-256

### Dir