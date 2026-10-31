# SOC Analyst Homelab — Progress Log

## Project Environment

**Platform:** Oracle VirtualBox
**SIEM:** Wazuh 4.14.9
**Endpoint:** Ubuntu Server 20.04.5 LTS (`jh-server`)
**Wazuh Agent:** 4.14.9, Agent ID `001`
**Network IDS:** Suricata 8.0.4

## 1. Lab Components and Connectivity

| Component | Role | Internal IP |
|---|---|---|
| Wazuh Server | SIEM and alert management | `192.168.77.10` |
| Target Server | Monitored endpoint | `192.168.77.20` |
| Kali Linux | Controlled security testing | `192.168.77.30` |

The virtual machines communicate through the VirtualBox Internal Network (`intnet`).

## 2. Validated Detection Results

### Wazuh Custom Rule

- Validated custom rule `100300`.
- Confirmed event collection from the target endpoint.
- Verified the resulting alert in Wazuh.

### Suricata Integration and Network Detection

- Confirmed Suricata event collection from `/var/log/suricata/eve.json`.
- Validated Wazuh processing of Suricata events using rule `86601`.
- Performed a controlled Nmap TCP SYN scan from Kali Linux.
- Validated custom Suricata signature `1000001`.
- Confirmed the corresponding Wazuh alert using rule `86601`.

### File Integrity Monitoring

| Rule ID | Detection |
|---|---|
| `554` | File added |
| `550` | File integrity checksum changed |

- Created a test file in the monitored endpoint directory.
- Modified the test file.
- Confirmed corresponding FIM alerts in Wazuh.

### SSH Authentication Monitoring

| Rule ID | Level | Detection |
|---|---:|---|
| `2502` | 10 | PAM 2 more authentication failures |
| `5710` | 5 | Login attempt using a non-existent user |
| `5503` | 5 | PAM login failure |

- Performed controlled failed SSH login attempts from Kali Linux.
- Confirmed related authentication alerts in Wazuh.
- Documented the test evidence.

## 3. Malware Detection — In Progress

- Installed ClamAV on the Ubuntu target.
- Created a harmless EICAR test file.
- Configured Wazuh Agent to collect the ClamAV test log.
- Created and validated custom Wazuh rule `100310`.
- Confirmed that Wazuh received the test log event.

**Limitation:** ClamAV signature database retrieval was unsuccessful during testing. Actual ClamAV detection of the EICAR file has not yet been validated.

## 4. Outstanding Items

- [ ] Restore ClamAV signature database availability and validate actual EICAR detection.
- [ ] Verify exact file-path attribution in FIM alerts.
- [ ] Validate Who-data attribution.
- [ ] Investigate the earlier audit event-dispatch warning.
- [ ] Validate Wazuh Active Response in the isolated lab.
- [ ] Expand incident investigation documentation.

## 5. Security Notes

- Never commit passwords, API keys, access tokens, or private keys.
- Review configuration files before publishing them.
- Do not upload raw Suricata logs or other potentially sensitive data.
- Use harmless and disposable test files for security experiments.
- Perform security testing only on systems owned by the lab operator or explicitly authorized for testing.

## 6. Summary

The lab has validated Wazuh endpoint event collection, custom-rule alerting, SSH authentication failure monitoring, File Integrity Monitoring, and Suricata-to-Wazuh network intrusion alert integration.

Malware detection, Who-data attribution, and Active Response remain subjects for further validation.
