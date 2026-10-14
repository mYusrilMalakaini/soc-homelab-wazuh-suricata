# SOC Analyst Homelab — Wazuh & Suricata

**Proyek laboratorium keamanan siber untuk mempelajari pemantauan keamanan, analisis log, deteksi ancaman, dan investigasi insiden menggunakan Wazuh dan Suricata.**

## 1. Tentang Proyek

Proyek ini merupakan simulasi lingkungan Security Operations Center (SOC) menggunakan mesin virtual di Oracle VirtualBox.

Wazuh digunakan untuk mengumpulkan dan menganalisis event keamanan dari endpoint, sedangkan Suricata digunakan untuk memantau lalu lintas jaringan dan menghasilkan event deteksi IDS.

Tujuan utama proyek ini adalah memahami alur kerja SIEM, memvalidasi aturan deteksi, menganalisis alert, serta mendokumentasikan hasil pengujian keamanan dalam lingkungan laboratorium yang terisolasi.

## 2. Lingkungan Laboratorium

| Komponen | Teknologi / Versi | Fungsi |
|---|---|---|
| SIEM | Wazuh Manager 4.5.4 | Pengumpulan dan analisis event keamanan |
| Agent | Wazuh Agent 4.5.4 | Mengirim event dari endpoint ke manager |
| IDS | Suricata | Pemantauan jaringan dan deteksi aktivitas mencurigakan |
| Server target | Ubuntu Server 20.04.5 LTS | Endpoint yang dipantau |
| Mesin pengujian | Kali Linux | Pengujian keamanan terkontrol |
| Virtualisasi | Oracle VirtualBox | Menjalankan mesin virtual |
| Akses jarak jauh | SSH melalui MobaXterm | Administrasi server |

## 3. Arsitektur Jaringan

Laboratorium menggunakan tiga mesin virtual yang terhubung melalui jaringan internal VirtualBox.

| Mesin Virtual | Peran | IP Internal |
|---|---|---|
| Wazuh Server | SIEM dan pengelolaan alert | `192.168.77.10` |
| Target Server | Endpoint dengan Wazuh Agent dan Suricata | `192.168.77.20` |
| Kali Linux | Mesin pengujian keamanan | `192.168.77.30` |

### Alur Pemantauan dan Deteksi

1. Server target menghasilkan log sistem dan event keamanan.
2. Suricata memantau lalu lintas jaringan dan menghasilkan event IDS.
3. Wazuh Agent meneruskan event yang dipantau ke Wazuh Manager.
4. Wazuh memproses event menggunakan decoder dan aturan deteksi.
5. Alert ditinjau melalui dashboard Wazuh untuk validasi dan investigasi.

## 4. Bukti Pengujian dan Validasi Deteksi

Bagian ini berisi dokumentasi pengujian yang telah dilakukan dan hasil deteksi yang teramati di dashboard Wazuh.

### 4.1 Custom Wazuh Rule — ID 100300

**Tujuan:** Memvalidasi kemampuan aturan Wazuh dalam mendeteksi event uji dari endpoint.

Sebuah pesan pengujian ditulis ke log sistem pada server target. Wazuh berhasil mendeteksi event tersebut menggunakan custom rule `100300`.

![Bukti custom Wazuh rule 100300](screenshots/01-wazuh-custom-rule-100300.png)

### 4.2 Integrasi Alert Suricata — Rule ID 86601

**Tujuan:** Memvalidasi bahwa event dari Suricata dapat diterima dan diproses oleh Wazuh.

Event Suricata teramati pada dashboard Wazuh dengan rule ID `86601`. Hal ini menunjukkan bahwa event IDS berhasil masuk ke alur pemantauan Wazuh.

![Bukti alert Suricata rule 86601](screenshots/02-suricata-alert-86601.png)

### 4.3 File Integrity Monitoring — File Baru, Rule ID 554

**Tujuan:** Memvalidasi deteksi pembuatan file pada direktori yang dipantau.

Sebuah file pengujian dibuat pada direktori home endpoint. Wazuh menghasilkan alert yang menunjukkan bahwa file baru telah terdeteksi.

![Bukti FIM file baru rule 554](screenshots/03-fim-file-added-554.png)

### 4.4 File Integrity Monitoring — Perubahan File, Rule ID 550

**Tujuan:** Memvalidasi deteksi perubahan integritas file yang dipantau.

File pengujian yang sama kemudian diubah. Wazuh menghasilkan alert perubahan integritas file.

![Bukti FIM perubahan file rule 550](screenshots/04-fim-file-modified-550.png)

## 5. Status Pengembangan

### Selesai

- [x] Menjalankan Wazuh Manager dan dashboard.
- [x] Memasang Wazuh Agent pada server target.
- [x] Mengonfigurasi konektivitas jaringan internal.
- [x] Memvalidasi penerimaan event dari endpoint.
- [x] Menguji custom Wazuh rule `100300`.
- [x] Mengamati event Suricata pada Wazuh melalui rule `86601`.
- [x] Memvalidasi deteksi file baru melalui rule `554`.
- [x] Memvalidasi deteksi perubahan file melalui rule `550`.
- [x] Mendokumentasikan bukti deteksi awal.

### Tahap Berikutnya

- [ ] Memastikan detail alert FIM, termasuk path file yang berubah.
- [ ] Memvalidasi atribusi pengguna melalui fitur Who-data.
- [ ] Menyelidiki peringatan audit event-dispatch yang muncul sebelumnya.
- [ ] Menguji custom Suricata rule `100200`.
- [ ] Memvalidasi Wazuh Active Response dalam jaringan lab terisolasi.
- [ ] Mendokumentasikan langkah investigasi, temuan, dan hasil respons.

## 6. Tujuan Pembelajaran

Melalui proyek ini, keterampilan yang ingin dikembangkan meliputi:

- Memahami konsep SIEM dan IDS.
- Mengumpulkan serta menganalisis log keamanan endpoint dan jaringan.
- Memahami proses deteksi melalui aturan Wazuh.
- Melakukan validasi alert menggunakan event pengujian terkontrol.
- Memahami File Integrity Monitoring (FIM) dan audit event.
- Melakukan investigasi awal terhadap aktivitas mencurigakan.
- Mendokumentasikan temuan teknis sebagai bagian dari proses penanganan insiden.

## 7. Teknologi yang Digunakan

- Wazuh
- Suricata
- Ubuntu Server
- Kali Linux
- Oracle VirtualBox
- Linux system logging dan audit tools
- Git dan GitHub

## 8. Catatan Keamanan

Proyek ini ditujukan untuk pembelajaran dan pengujian dalam lingkungan laboratorium terisolasi. Seluruh aktivitas pengujian dilakukan hanya pada sistem yang dimiliki sendiri atau yang penggunaannya telah mendapatkan izin.

Konfigurasi yang mengandung kredensial, API key, kata sandi, atau informasi sensitif lainnya tidak boleh dipublikasikan ke repository.

## 9. Dokumentasi Progres

Catatan perkembangan pengerjaan laboratorium tersedia di [Dokumentasi Progres](docs/progress-log.md).
