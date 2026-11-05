# SOC Analyst Learning Journal

Folder untuk menyimpan catatan belajar dan hasil investigasi SOC Analyst dari homelab Wazuh dan Suricata.

## Struktur
- `learning/` — konsep dan materi pembelajaran.
- `investigations/` — analisis alert dan kasus.

## Cara menggunakan
1. Tambahkan materi baru ke `learning/`.
2. Buat satu file untuk setiap kasus di `investigations/`.
3. Pisahkan **observed facts**, **hypothesis**, dan **conclusion**.
4. Jangan simpan password, token, private key, atau credential.
5. Tandai hasil yang sudah tervalidasi dan yang masih berupa hipotesis.

## Lingkungan lab
| Komponen | Hostname | IP |
|---|---|---|
| Wazuh Manager | `wazuh-server` | `192.168.77.10` |
| Ubuntu target | `jh-server` | `192.168.77.20` |
| Kali Linux | `kali` | `192.168.77.30` |

Virtualisasi: VirtualBox, Internal Network `intnet`, subnet `192.168.77.0/24`.
