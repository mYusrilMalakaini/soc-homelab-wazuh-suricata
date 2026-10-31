\# Network Intrusion Detection — Suricata and Wazuh



\## 1. Objective



Validate Suricata's ability to detect a controlled TCP SYN scan and verify that the resulting alert is forwarded to and processed by Wazuh.



\## 2. Lab Configuration



| Component | Details |

|---|---|

| Source | Kali Linux (`192.168.77.30`) |

| Target | Ubuntu Server (`192.168.77.20`) |

| Network interface | `enp0s3` |

| IDS | Suricata 8.0.4 |

| SIEM | Wazuh 4.14.9 |



\## 3. Test Procedure



A TCP SYN scan was performed from Kali Linux against the Ubuntu target using Nmap:



```bash

nmap -sS -T3 --top-ports 100 192.168.77.20

```



A custom Suricata rule was configured to detect at least 10 TCP SYN packets from the Kali source within 10 seconds.



\## 4. Detection Results



| Detection component | Result |

|---|---|

| Suricata custom signature ID | `1000001` |

| Suricata signature | `LOCAL DETECT Kali Linux TCP SYN Scan` |

| Wazuh rule ID | `86601` |

| Wazuh alert level | `3` |

| Source IP | `192.168.77.30` |

| Destination IP | `192.168.77.20` |

| Event log | `/var/log/suricata/eve.json` |



Suricata generated an alert for the test traffic. The event was collected by the Wazuh Agent and processed by Wazuh Manager using rule `86601`.



\## 5. Evidence



\### Suricata Alert in Wazuh



!\[Suricata alert](../../screenshots/suricata/09-wazuh-suricata-alert.png)



\### TCP SYN Scan Detection



!\[TCP SYN scan alert](../../screenshots/suricata/10-wazuh-suricata-syn-scan-alert.png)



\## 6. Conclusion



The controlled TCP SYN scan was detected by the custom Suricata rule and the resulting event was successfully processed by Wazuh.



This validates the Suricata-to-Wazuh event integration and the configured detection rule in the lab environment. The test represents controlled reconnaissance activity, not evidence of a real-world attack.



Suricata operated in IDS mode; the alert action was `allowed`, meaning the traffic was detected but not blocked.
