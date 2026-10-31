# SOC Analyst Homelab — Wazuh & Suricata

A cybersecurity home lab project for learning SIEM operations, endpoint monitoring, network intrusion detection, security event analysis, and incident investigation using Wazuh and Suricata.

## 1. Project Overview

This project simulates a small Security Operations Center (SOC) environment using virtual machines in Oracle VirtualBox.

- **Wazuh** provides endpoint security monitoring, log collection, event analysis, and alert management.
- **Suricata** monitors network traffic and generates Network Intrusion Detection System (NIDS) alerts.
- **Kali Linux** is used to perform controlled security tests against the lab target.

The main objective is to understand how security events are generated, collected, detected, and investigated in a controlled environment.

## 2. Lab Architecture

| Component | Technology | Role |
|---|---|---|
| SIEM | Wazuh Manager 4.14.9 | Event analysis and alert management |
| Endpoint Agent | Wazuh Agent 4.14.9 | Endpoint log collection |
| IDS | Suricata 8.0.4 | Network traffic monitoring |
| Target | Ubuntu Server 20.04.5 LTS | Monitored endpoint |
| Testing Machine | Kali Linux | Controlled security testing |
| Virtualization | Oracle VirtualBox | Virtual lab environment |

### Network Topology

| Virtual Machine | Internal IP | Role |
|---|---|---|
| Wazuh Server | `192.168.77.10` | SIEM |
| Target Server | `192.168.77.20` | Wazuh Agent and Suricata |
| Kali Linux | `192.168.77.30` | Security testing |

- **Network:** `192.168.77.0/24`
- **VirtualBox network:** Internal Network (`intnet`)

See [Lab Architecture](docs/architecture/lab-architecture.md) for details.

## 3. Validated Detection Scenarios

| Scenario | Detection | Result |
|---|---|---|
| Custom Wazuh Rule | Rule ID `100300` | Validated |
| Suricata Event Integration | Wazuh Rule ID `86601` | Validated |
| File Integrity Monitoring | Rule IDs `554`, `550` | Validated |
| SSH Authentication Failures | Rule IDs `2502`, `5710`, `5503` | Validated |
| TCP SYN Scan Detection | Suricata SID `1000001` | Validated |

These results represent controlled tests performed in the lab, not confirmed real-world attacks.

### Detection Documentation

- [SSH Authentication Failure Detection](docs/detections/ssh-authentication.md)
- [File Integrity Monitoring](docs/detections/file-integrity-monitoring.md)
- [Network Intrusion Detection](docs/detections/network-intrusion.md)

## 4. Evidence

Screenshots from the lab are organized by category:

- [Wazuh Evidence](screenshots/wazuh/)
- [Suricata Evidence](screenshots/suricata/)
- [Testing Evidence](screenshots/testing/)

## 5. Project Status

### Validated

- [x] Wazuh Manager and Dashboard deployment
- [x] Wazuh Agent connectivity
- [x] Custom Wazuh rule testing
- [x] Suricata event collection by Wazuh
- [x] File creation and modification detection
- [x] SSH authentication failure monitoring
- [x] Controlled TCP SYN scan detection

### Planned / Further Validation

- [ ] Validate ClamAV-based EICAR detection with a functioning signature database
- [ ] Validate exact FIM file-path attribution
- [ ] Validate Who-data attribution
- [ ] Investigate the earlier audit event-dispatch warning
- [ ] Validate Wazuh Active Response in the isolated lab
- [ ] Expand incident investigation documentation

## 6. Repository Structure

```text
soc-homelab-wazuh-suricata/
├── README.md
├── docs/
│   ├── architecture/
│   │   └── lab-architecture.md
│   ├── detections/
│   │   ├── ssh-authentication.md
│   │   ├── file-integrity-monitoring.md
│   │   └── network-intrusion.md
│   └── progress-log.md
└── screenshots/
    ├── wazuh/
    ├── suricata/
    └── testing/
```

## 7. Learning Objectives

This project is intended to develop practical skills in:

- SIEM and IDS fundamentals
- Endpoint and network security monitoring
- Security event and alert analysis
- Detection rule validation
- File Integrity Monitoring
- Initial incident investigation and documentation

## 8. Security Notes

This repository documents a controlled cybersecurity lab. All testing is limited to systems owned by the lab operator or systems for which explicit authorization has been granted.

Never publish passwords, API keys, access tokens, private keys, or other sensitive information. Raw logs should be reviewed and sanitized before publication.

## 9. Progress Log

See [Project Progress Log](docs/progress-log.md) for development notes and validation history.
