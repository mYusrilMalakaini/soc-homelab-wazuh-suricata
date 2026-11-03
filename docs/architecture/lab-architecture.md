# Arsitektur SOC Homelab

## 1. Gambaran Umum

Project ini menggunakan lingkungan virtual terisolasi yang dibangun menggunakan Oracle VirtualBox untuk menyimulasikan lingkungan Security Operations Center (SOC) skala kecil.

Lab ini mengintegrasikan Wazuh sebagai platform Security Information and Event Management (SIEM), Suricata sebagai Network Intrusion Detection System (IDS), dan ClamAV sebagai antivirus untuk pemindaian file serta deteksi berdasarkan signature.

Selain itu, terdapat beberapa kemampuan deteksi tambahan yang diimplementasikan melalui Wazuh File Integrity Monitoring (FIM), custom detection rules, dan script lokal untuk pencocokan hash IOC.

Lingkungan ini dirancang untuk menyimulasikan pemantauan keamanan endpoint dan jaringan, pengujian keamanan terkontrol, pengumpulan event, serta analisis alert.

## 2. Komponen Lab

| Virtual Machine | Peran | IP Internal |
|---|---|---|
| Wazuh Server | SIEM, analisis event, dan pengelolaan alert | `192.168.77.10` |
| Target Server | Endpoint Ubuntu yang menjalankan Wazuh Agent, Suricata, dan ClamAV | `192.168.77.20` |
| Kali Linux | Mesin untuk melakukan pengujian keamanan terkontrol | `192.168.77.30` |

**Jaringan internal:** `192.168.77.0/24`

**Mode jaringan VirtualBox:** Internal Network (`intnet`)

### Perangkat Keamanan

| Perangkat | Fungsi |
|---|---|
| Wazuh Manager | Analisis event dan pengelolaan alert terpusat |
| Wazuh Agent | Pengumpulan dan pengiriman log dari endpoint |
| Suricata | Deteksi intrusi pada lalu lintas jaringan |
| ClamAV | Pemindaian antivirus dan deteksi berdasarkan signature |
| IOC Hash Script | Pencocokan hash SHA256 dengan daftar IOC lokal |
| Wazuh FIM | Pemantauan integritas file |

## 3. Tanggung Jawab Setiap Komponen

### Wazuh Server

- Menerima event keamanan dari endpoint yang dipantau.
- Memproses event menggunakan decoder dan detection rules.
- Menerapkan custom detection rules untuk event ClamAV dan IOC hash.
- Menghasilkan alert untuk kebutuhan pemantauan dan investigasi keamanan.
- Menyediakan Wazuh Dashboard untuk meninjau alert.
- Memusatkan event keamanan dari endpoint dan jaringan.

### Target Server

Target Server berbasis Ubuntu berfungsi sebagai endpoint utama yang dipantau.

Tanggung jawabnya meliputi:

- Menjalankan Wazuh Agent untuk mengirimkan log yang telah dikonfigurasi.
- Menjalankan Suricata untuk memeriksa lalu lintas jaringan.
- Menjalankan ClamAV untuk memindai file berdasarkan database signature antivirus.
- Menyediakan log autentikasi SSH dan aktivitas sistem.
- Memantau perubahan file menggunakan Wazuh File Integrity Monitoring (FIM).
- Menjalankan script lokal untuk pencocokan hash IOC.
- Menyimpan event Suricata di `/var/log/suricata/eve.json`.
- Menyimpan hasil pemindaian ClamAV di `/var/log/clamav/eicar-scan.log`.
- Menyimpan event pencocokan hash IOC di `/var/log/ioc-lab/hash-detection.log`.

### Kali Linux

Kali Linux berfungsi sebagai mesin untuk melakukan pengujian keamanan secara terkontrol.

Tanggung jawabnya meliputi:

- Menghasilkan percobaan kegagalan autentikasi SSH terhadap Target Server.
- Melakukan pengujian network reconnaissance menggunakan Nmap.
- Menghasilkan percobaan koneksi TCP berulang menuju target.
- Menyimulasikan aktivitas keamanan untuk memvalidasi detection rules.

Seluruh aktivitas pengujian dibatasi pada lingkungan lab yang terisolasi.

## 4. Alur Pengumpulan dan Deteksi Event

Lab ini menggunakan beberapa sumber deteksi untuk menghasilkan event keamanan.

### Alur Pemantauan Endpoint

1. Kali Linux menghasilkan percobaan kegagalan autentikasi SSH terhadap Ubuntu Target Server.
2. Target Server mencatat event autentikasi pada log sistem.
3. Wazuh Agent mengumpulkan log autentikasi yang telah dikonfigurasi.
4. Wazuh Manager memproses event menggunakan decoder dan detection rules.
5. Alert ditinjau melalui Wazuh Dashboard.

### Alur File Integrity Monitoring

1. File baru dibuat atau file yang sudah ada dimodifikasi pada Target Server.
2. Wazuh FIM memantau direktori yang telah dikonfigurasi.
3. Perubahan file menghasilkan event pemantauan integritas.
4. Wazuh Manager memproses event dan menghasilkan alert yang sesuai.
5. Alert ditinjau melalui Wazuh Dashboard.

### Alur Deteksi Malware

1. ClamAV memindai file pengujian menggunakan database signature antivirus.
2. ClamAV mencatat hasil pemindaian ke `/var/log/clamav/eicar-scan.log`.
3. Wazuh Agent memantau dan mengirimkan log ClamAV yang telah dikonfigurasi.
4. Wazuh Manager memproses event menggunakan custom detection rule `100311`.
5. Alert yang dihasilkan ditinjau melalui Wazuh Dashboard.

Integrasi deteksi malware divalidasi menggunakan file uji antivirus EICAR. EICAR merupakan string pengujian antivirus yang aman dan bukan sampel malware aktif.

### Alur Network Intrusion Detection

1. Kali Linux menghasilkan lalu lintas jaringan terkontrol menuju Ubuntu Target Server.
2. Suricata memeriksa lalu lintas jaringan melalui interface yang telah dikonfigurasi.
3. Suricata menghasilkan event IDS dan menyimpannya di `/var/log/suricata/eve.json`.
4. Wazuh Agent mengumpulkan event Suricata dalam format JSON.
5. Wazuh Manager memproses event menggunakan detection rules.
6. Alert jaringan ditinjau melalui Wazuh Dashboard.

### Alur IOC Hash Detection

1. File pengujian dibuat pada Target Server.
2. Script IOC lokal menghitung hash SHA256 file tersebut.
3. Hash yang diperoleh dibandingkan dengan daftar hash IOC lokal.
4. Script mencatat hasil pencocokan ke `/var/log/ioc-lab/hash-detection.log`.
5. Wazuh Agent mengumpulkan event dari file log tersebut.
6. Wazuh Manager memproses event menggunakan custom detection rule `100320`.
7. Event yang memiliki kecocokan IOC menghasilkan alert untuk dianalisis.

## 5. Isolasi Jaringan

Ketiga virtual machine saling terhubung melalui VirtualBox Internal Network dengan nama `intnet`.

Jaringan internal menggunakan subnet:

`192.168.77.0/24`

Konfigurasi ini menyediakan lingkungan terisolasi untuk melakukan pengujian dan pemantauan keamanan secara terkontrol.

Aktivitas pengujian harus dibatasi pada sistem yang dimiliki oleh pengelola lab atau sistem yang telah mendapatkan izin eksplisit untuk diuji.

## 6. Cakupan Implementasi Arsitektur

Lab saat ini telah berhasil memvalidasi beberapa kemampuan deteksi berikut:

- Pemantauan kegagalan autentikasi SSH menggunakan Wazuh.
- File Integrity Monitoring (FIM) untuk mendeteksi penambahan dan perubahan file.
- Deteksi signature antivirus menggunakan ClamAV dengan file uji EICAR.
- Integrasi log ClamAV ke Wazuh menggunakan custom detection rule `100311`.
- Deteksi intrusi jaringan menggunakan Suricata dan Wazuh.
- Custom Suricata rule untuk mendeteksi aktivitas TCP SYN scan terkontrol.
- Pencocokan hash SHA256 terhadap IOC menggunakan script lokal dan custom Wazuh rule `100320`.
- Deteksi aktivitas jaringan mencurigakan berupa percobaan koneksi TCP berulang menuju port yang dipantau.

Lab ini menunjukkan proses pengumpulan event keamanan, deteksi, dan peninjauan alert secara terpusat melalui Wazuh.

Beberapa kemampuan berikut belum divalidasi sepenuhnya dan masih berada di luar cakupan implementasi yang telah diselesaikan:

- Atribusi Who-data untuk event integritas file.
- Wazuh Active Response.
- Otomatisasi containment dan remediasi insiden.
- Pengujian siklus Incident Response secara menyeluruh.
- Deteksi sampel malware nyata di luar file uji EICAR.

Pengembangan selanjutnya dapat mencakup alert triage, investigasi insiden, integrasi threat intelligence, penyempurnaan detection rules, dan alur automated response.