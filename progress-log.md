# SOC Analyst Homelab — Progress Log

## Session: SIEM Integration and Validation

**Date:** October 14, 2026  
**Platform:** VirtualBox  
**Status:** Core detections validated

### 1. Lab Components

- **SIEM:** Wazuh Manager and Dashboard
- **Endpoint:** Ubuntu Server (`jh-server`)
- **Wazuh Agent:** `JH`, Agent ID `001`, version `4.5.4`
- **Network Monitoring:** Suricata
- **Virtualization:** Oracle VirtualBox

### 2. Validation Results

#### Wazuh Agent Connectivity

- Confirmed that the Wazuh Manager service was active.
- Confirmed that Agent `001` (`JH`) was active.
- Accessed the Wazuh Dashboard from the host machine.
- Generated and verified a custom connectivity test alert.

**Evidence:**
- Rule ID: `100300`
- Rule level: `5`
- Description: `SOC Lab: Wazuh agent connectivity test event detected`
- Log source: `/var/log/syslog`

#### Suricata Integration

- Observed a Suricata-related event in Wazuh Security Events.
- Confirmed event collection from `/var/log/suricata/eve.json`.

**Observed rule:** `86601`

#### File Integrity Monitoring (FIM)

- Confirmed that the Wazuh Agent and `auditd` services were running.
- Created and modified a test file under `/home/jadihacker`.
- Observed a file-addition alert and a checksum-change alert in Wazuh.

| Rule ID | Detection |
|---|---|
| `554` | File added to the system |
| `550` | Integrity checksum changed |

### 3. Outstanding Items

- [ ] Verify the exact file path in the FIM alert fields.
- [ ] Validate Who-data attribution.
- [ ] Investigate an earlier audit dispatch queue warning.
- [ ] Capture sanitized screenshots as evidence.
- [ ] Continue testing active response and malware detection.

### 4. Security Notes

- Never commit passwords, API keys, access tokens, or private keys.
- Review configuration files before publishing them.
- Do not upload raw Suricata logs or other potentially large log files.
- Use disposable test files for security experiments.

### 5. Summary

The lab has demonstrated Wazuh agent connectivity, custom-rule alerting, Suricata event collection, and FIM detection for file addition and checksum changes. Further validation is needed for Who-data attribution and audit dispatch reliability.