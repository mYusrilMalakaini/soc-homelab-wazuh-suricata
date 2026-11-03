# SOC Analyst Homelab — Wazuh & Suricata

Proyek *cybersecurity home lab* untuk mempelajari operasi Security Information and Event Management (SIEM), pemantauan endpoint, deteksi intrusi jaringan, analisis event keamanan, dan investigasi insiden menggunakan Wazuh, Suricata, dan ClamAV.

## 1. Gambaran Umum Proyek

Proyek ini menyimulasikan lingkungan Security Operations Center (SOC) sederhana menggunakan beberapa virtual machine di Oracle VirtualBox.

- **Wazuh** digunakan untuk pemantauan keamanan endpoint, pengumpulan log, analisis event, dan pengelolaan alert.
- **Suricata** digunakan untuk memantau lalu lintas jaringan dan menghasilkan alert Network Intrusion Detection System (NIDS).
- **ClamAV** digunakan untuk melakukan pemindaian file dan mendeteksi file uji antivirus EICAR.
- **Kali Linux** digunakan untuk melakukan pengujian keamanan terkontrol terhadap server target.

Tujuan utama proyek ini adalah memahami bagaimana event keamanan dihasilkan, dikumpulkan, dideteksi, dan dianalisis dalam lingkungan pengujian yang terkontrol.

## 2. Arsitektur Lab

| Komponen | Teknologi | Peran |
|---|---|---|
| SIEM | Wazuh Manager 4.14.9 | Analisis event dan pengelolaan alert |
| Endpoint Agent | Wazuh Agent 4.14.9 | Pengumpulan log endpoint |
| IDS | Suricata 8.0.4 | Pemantauan lalu lintas jaringan |
| Antivirus | ClamAV 1.4.6 | Pemindaian file dan deteksi EICAR |
| Target | Ubuntu Server 20.04.5 LTS | Endpoint yang dipantau |
| Mesin Pengujian | Kali Linux | Pengujian keamanan terkontrol |
| Virtualisasi | Oracle VirtualBox | Lingkungan virtual lab |

### Topologi Jaringan

| Virtual Machine | IP Internal | Peran |
|---|---|---|
| Wazuh Server | `192.168.77.10` | SIEM |
| Target Server | `192.168.77.20` | Wazuh Agent, Suricata, dan ClamAV |
| Kali Linux | `192.168.77.30` | Pengujian keamanan |

- **Jaringan:** `192.168.77.0/24`
- **VirtualBox Network:** Internal Network (`intnet`)

Lihat [Arsitektur Lab](docs/architecture/lab-architecture.md) untuk penjelasan lebih lanjut.

## 3. Skenario Deteksi

### 3.1 Skenario yang Telah Divalidasi

| Skenario | Deteksi | Hasil |
|---|---|---|
| Custom Wazuh Rule | Rule ID `100300` | Tervalidasi |
| Integrasi Event Suricata | Wazuh Rule ID `86601` | Tervalidasi |
| File Integrity Monitoring | Rule ID `554`, `550` | Tervalidasi |
| Kegagalan Autentikasi SSH | Rule ID `2502`, `5710`, `5503` | Tervalidasi |
| Deteksi TCP SYN Scan | Suricata SID `1000001` | Tervalidasi |
| Deteksi Malware dengan ClamAV | Wazuh Rule ID `100311`, Level 10 | Tervalidasi |
| Deteksi IOC Berdasarkan Hash | Wazuh Rule ID `100320`, Level 10 | Tervalidasi |
| Suspicious Network Activity | Suricata SID `1000002`, Wazuh Rule ID `86601` | Alert teramati; validasi threshold perlu dilanjutkan |

Pengujian ClamAV menggunakan file EICAR sebagai file uji antivirus standar. ClamAV berhasil mendeteksi signature `Eicar-Test-Signature` dan Wazuh menghasilkan alert terkait.

Seluruh hasil tersebut berasal dari pengujian terkontrol di lingkungan lab dan bukan merupakan bukti terjadinya serangan nyata.

### 3.2 Dokumentasi Deteksi

- [Deteksi Kegagalan Autentikasi SSH](docs/detections/ssh-authentication.md)
- [File Integrity Monitoring](docs/detections/file-integrity-monitoring.md)
- [Network Intrusion Detection — TCP SYN Scan](docs/detections/network-intrusion.md)
- [Malware Detection — ClamAV EICAR](docs/detections/malware-detection.md)
- [Deteksi IOC / Hash File Mencurigakan](docs/detections/ioc-hash-detection.md)
- [Suspicious Network Activity](docs/detections/suspicious-network-activity.md)

## 4. Bukti Pengujian

Screenshot hasil pengujian dikelompokkan berdasarkan kategori dan skenario.

- [Bukti Wazuh](screenshots/wazuh/)
- [Bukti Suricata](screenshots/suricata/)
- [Bukti Pengujian SSH](screenshots/testing/)
- [Bukti Deteksi Malware EICAR](screenshots/case-02-malware-detection/)
- [Bukti Deteksi IOC Hash](screenshots/case-04-ioc-hash-detection/)
- [Bukti Suspicious Network Activity](screenshots/case-05-suspicious-network-activity/)

**Bukti Suspicious Network Activity:**

- [Wazuh — daftar event mencurigakan](screenshots/case-05-suspicious-network-activity/16-wazuh-suspicious-network-events.png)
- [Wazuh — detail alert](screenshots/case-05-suspicious-network-activity/17-wazuh-suspicious-network-details.png)
- [Suricata — alert koneksi TCP ke port 4444](screenshots/case-05-suspicious-network-activity/18-suricata-suspicious-network-alert.png)

Dokumentasi pada setiap skenario juga memuat gambar bukti pengujian yang relevan.

## 5. Status Proyek

### Telah Divalidasi

- [x] Deployment Wazuh Manager dan Dashboard
- [x] Konektivitas Wazuh Agent
- [x] Pengujian custom Wazuh rule
- [x] Pengumpulan event Suricata oleh Wazuh
- [x] Deteksi pembuatan dan modifikasi file melalui FIM
- [x] Pemantauan kegagalan autentikasi SSH
- [x] Deteksi TCP SYN scan terkontrol
- [x] Deteksi EICAR menggunakan ClamAV
- [x] Pengiriman log hasil pemindaian ClamAV ke Wazuh
- [x] Deteksi IOC berdasarkan pencocokan hash SHA-256
- [x] Deteksi event koneksi TCP ke port 4444 melalui Suricata dan Wazuh

### Dalam Proses atau Validasi Lanjutan

- [ ] Validasi threshold rule Suspicious Network Activity (5 koneksi dalam 10 detik) melalui pengujian terukur
- [ ] Validasi konfigurasi ClamAV daemon dan pemindaian otomatis atau real-time
- [ ] Menambahkan atribusi path file yang lebih spesifik pada hasil pengujian FIM
- [ ] Mengeksplorasi konfigurasi FIM Who-data
- [ ] Meninjau peringatan terkait audit event dispatch
- [ ] Mengimplementasikan dan menguji Wazuh Active Response
- [ ] Melengkapi dokumentasi investigasi insiden berdasarkan alert yang telah dihasilkan

## 6. Catatan Keamanan dan Batasan Pengujian

- Seluruh pengujian dilakukan dalam lingkungan VirtualBox terisolasi menggunakan jaringan internal.
- Percobaan login SSH gagal, pemindaian Nmap, dan koneksi TCP ke port 4444 merupakan aktivitas simulasi terkontrol.
- Pengujian IOC menggunakan hash buatan sendiri, bukan hash malware yang telah diverifikasi.
- File EICAR merupakan file uji antivirus standar dan bukan malware aktif.
- Pengujian ClamAV menggunakan pemindaian manual dengan `clamscan`. Hasil ini belum membuktikan bahwa perlindungan real-time atau ClamAV daemon telah berjalan.
- Pada pengujian Suricata, nilai `action: allowed` menunjukkan bahwa event terdeteksi tetapi trafik tidak diblokir oleh rule tersebut.
- Alert Wazuh menunjukkan event berhasil diproses dan dideteksi; alert tidak otomatis berarti aktivitas berhasil diblokir.
- Pada Suspicious Network Activity, alert dengan SID `1000002` dan Wazuh Rule ID `86601` telah teramati. Namun, bukti yang tersedia belum memastikan bahwa threshold 5 koneksi dalam 10 detik terpenuhi.
- Hasil pengujian hanya dinyatakan tervalidasi sesuai ruang lingkup bukti yang benar-benar diamati.

## 7. Struktur Repository

```text
soc-homelab-wazuh-suricata/
├── README.md
├── docs/
│   ├── architecture/
│   │   └── lab-architecture.md
│   ├── detections/
│   │   ├── file-integrity-monitoring.md
│   │   ├── ioc-hash-detection.md
│   │   ├── malware-detection.md
│   │   ├── network-intrusion.md
│   │   ├── ssh-authentication.md
│   │   └── suspicious-network-activity.md
│   └── progress-log.md
└── screenshots/
    ├── case-02-malware-detection/
    ├── case-04-ioc-hash-detection/
    ├── case-05-suspicious-network-activity/
    ├── suricata/
    ├── testing/
    └── wazuh/
```

## 8. Ringkasan

SOC home lab ini telah mengintegrasikan Wazuh sebagai platform SIEM dan Suricata sebagai Network Intrusion Detection System. ClamAV juga telah digunakan untuk melakukan pemindaian file uji EICAR dan mengirimkan hasil deteksinya ke Wazuh.

Pengujian yang telah dilakukan mencakup kegagalan autentikasi SSH, File Integrity Monitoring, TCP SYN scan, deteksi EICAR, pencocokan IOC berbasis SHA-256, serta deteksi koneksi TCP mencurigakan ke port 4444.

Pengembangan selanjutnya difokuskan pada penyelesaian validasi threshold Suspicious Network Activity, pengujian ClamAV daemon, implementasi Active Response, dan pendalaman investigasi insiden.