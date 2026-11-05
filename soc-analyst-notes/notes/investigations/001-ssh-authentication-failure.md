# Investigation 001 — SSH Authentication Failure

**Status:** In progress  
**Environment:** Controlled homelab simulation

## 1. Alert information
| Field | Nilai |
|---|---|
| Wazuh index | `wazuh-alerts-4.x-2026.10.17` |
| Agent | `jh-server` |
| Agent ID | `001` |
| Agent IP | `192.168.77.20` |
| Source IP (`rhost`) | `192.168.77.30` |
| Program | `sshd` |
| Decoder | `sshd` |
| Location | `journald` |
| Rule ID | `2502` |
| Rule Level | `10` |
| Rule description | `syslog: User missed the password more than one time` |
| MITRE ATT&CK | `T1110 — Brute Force` |
| Event timestamp | `2026-10-17T19:12:15.760+0000` |

## 2. Raw log
```text
Oct 17 19:12:14 jh-server sshd[5006]: PAM 2 more authentication failures; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.77.30
```

## 3. Observed facts
- Event berasal dari program `sshd`.
- Hostname yang tercatat adalah `jh-server`.
- Remote host (`rhost`) adalah `192.168.77.30`.
- Pesan menyatakan `PAM 2 more authentication failures`.
- Wazuh memicu Rule ID `2502` dengan Level `10`.
- Event dipetakan ke MITRE ATT&CK `T1110`.

## 4. Analyst hypotheses
_Isi setelah memeriksa bukti tambahan. Jangan tulis hipotesis sebagai fakta._
-

## 5. Investigation steps and results
| Waktu | Pemeriksaan | Hasil | Status bukti |
|---|---|---|---|
| | Pemeriksaan alert Rule `2502` | Alert JSON tersedia | Observed |
| | Cari alert Rule `5710` | Belum dicatat | Pending |
| | Cari alert Rule `5503` | Belum dicatat | Pending |
| | Periksa event SSH lain di sekitar waktu kejadian | Belum dicatat | Pending |

## 6. Event correlation
Catat event terkait dari source dan destination yang sama. Pastikan rentang waktu dan timezone konsisten.
- Rule `5710`:
- Rule `5503`:
- Event SSH lainnya:

## 7. MITRE ATT&CK mapping
- Technique: `T1110 — Brute Force`
- Tactic: `Credential Access`

Mapping ini merupakan konteks teknik yang terkait dengan deteksi, bukan bukti otomatis bahwa serangan berhasil.

## 8. Analyst assessment
- Classification: _Belum ditetapkan_
- Severity: Rule Level `10` (Wazuh)
- Priority: _Belum ditetapkan_
- Confidence: _Belum ditetapkan_
- Rationale: _Lengkapi berdasarkan bukti dan konteks_

## 9. Recommended actions
_Tentukan setelah korelasi dan validasi sumber selesai._

## 10. Final conclusion
_Belum ditulis — tunggu hasil investigasi._

## 11. Lessons learned
-
