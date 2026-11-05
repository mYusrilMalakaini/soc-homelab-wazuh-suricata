# Materi 02 — Alert Triage

**Tanggal mulai:** 5 November 2026  
**Level:** Beginner  
**SIEM:** Wazuh

## 1. Tujuan pembelajaran
Memahami pemeriksaan awal security alert, validasi bukti, penilaian urgensi, dan keputusan tindak lanjut.

## 2. Pengertian
Alert triage adalah proses pemeriksaan awal terhadap alert keamanan untuk memahami konteksnya, menilai kepentingannya, dan menentukan langkah selanjutnya.

## 3. Istilah penting
| Istilah | Pengertian |
|---|---|
| True Positive (TP) | Alert benar mendeteksi aktivitas yang sesuai dengan kondisi deteksi. |
| False Positive (FP) | Alert muncul, tetapi aktivitas sebenarnya bukan kondisi yang ingin dideteksi. |
| True Negative (TN) | Tidak ada alert dan memang tidak ada aktivitas yang seharusnya dideteksi. |
| False Negative (FN) | Aktivitas yang seharusnya terdeteksi tidak menghasilkan alert. |

True Positive untuk sebuah rule tidak otomatis berarti serangan berhasil atau pelakunya berbahaya.

## 4. Alur dasar alert triage
1. Identify alert.
2. Review details.
3. Validate evidence.
4. Assess severity and priority.
5. Decide next action: lanjut investigasi, eskalasi, atau penutupan dengan alasan terdokumentasi.

## 5. Contoh dari homelab
| Field | Nilai |
|---|---|
| Agent | `jh-server` |
| Agent IP | `192.168.77.20` |
| Source IP (`rhost`) | `192.168.77.30` |
| Rule ID | `2502` |
| Rule Level | `10` |
| Decoder | `sshd` |
| Location | `journald` |
| MITRE ATT&CK | `T1110 — Brute Force` |

Raw log:
```text
Oct 17 19:12:14 jh-server sshd[5006]: PAM 2 more authentication failures; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.77.30
```

Pengujian ini berasal dari simulasi login gagal yang dikendalikan di dalam lab.

## 6. Observed facts vs hypothesis

### Observed facts
- Program yang tercatat adalah `sshd`.
- Hostname pada log adalah `jh-server`.
- `rhost` menunjukkan `192.168.77.30`.
- Pesan menyebut dua kegagalan autentikasi tambahan.
- Wazuh menghasilkan Rule ID `2502`, Level `10`.

### Hypotheses yang memerlukan pemeriksaan
- Aktivitas mungkin merupakan brute-force attempt.
- Pengguna mungkin salah memasukkan password berulang kali.
- Sumber mungkin merupakan mesin pengujian yang sah.

Jangan mengubah hipotesis menjadi kesimpulan tanpa bukti pendukung.

## 7. Severity vs priority
- **Severity** menggambarkan tingkat keparahan teknis atau dampak yang terkait dengan alert.
- **Priority** menggambarkan urgensi penanganan berdasarkan konteks, dampak, aset, dan kemungkinan ancaman.
- Rule Level Wazuh bukan otomatis tingkat prioritas insiden.

## 8. Checklist analisis
- [ ] Identifikasi rule ID dan deskripsi.
- [ ] Catat timestamp dan timezone.
- [ ] Identifikasi agent dan host target.
- [ ] Identifikasi source dan destination jika tersedia.
- [ ] Baca `full_log` atau raw event.
- [ ] Pisahkan fakta dan hipotesis.
- [ ] Cari event terkait untuk korelasi.
- [ ] Pertimbangkan false positive dan keterbatasan bukti.
- [ ] Dokumentasikan keputusan dan alasan.

## 9. Pemahaman saya
_Tulis ulang konsep dengan kata-kata sendiri._

## 10. Pertanyaan yang belum dipahami
-

## 11. Referensi
- https://documentation.wazuh.com/current/
- https://documentation.wazuh.com/current/user-manual/ruleset/
- https://attack.mitre.org/techniques/T1110/
