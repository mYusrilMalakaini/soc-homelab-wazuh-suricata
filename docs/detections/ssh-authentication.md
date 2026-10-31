\# SSH Authentication Failure Detection



\## 1. Objective



Validate Wazuh's ability to collect SSH authentication logs and generate alerts for controlled failed login attempts.



\## 2. Lab Configuration



| Component | Details |

|---|---|

| Source | Kali Linux (`192.168.77.30`) |

| Target | Ubuntu Server (`192.168.77.20`) |

| Wazuh Agent | `jh-server` |

| SIEM | Wazuh 4.14.9 |



\## 3. Test Procedure



A controlled SSH authentication failure test was performed from Kali Linux against the Ubuntu target.



The test intentionally used unsuccessful authentication attempts to generate SSH/PAM authentication events.



\## 4. Detection Results



| Wazuh Rule ID | Level | Description |

|---|---:|---|

| `2502` | 10 | PAM 2 more authentication failures |

| `5710` | 5 | Login attempt using a non-existent user |

| `5503` | 5 | PAM login failure |



\*\*MITRE ATT\&CK:\*\* `T1110` — Brute Force, as displayed in the related alert.



\## 5. Evidence



\### Wazuh SSH Authentication Alert



!\[SSH authentication alert](../../screenshots/wazuh/05-wazuh-ssh-brute-force.png)



\### Authentication Events



!\[SSH authentication events](../../screenshots/wazuh/06-wazuh-ssh-auth-failure.png)



\### SSH Test from Kali Linux



!\[Kali SSH failed login](../../screenshots/testing/07-kali-ssh-failed-login.png)



\### Wazuh Authentication Overview



!\[Wazuh authentication overview](../../screenshots/wazuh/08-wazuh-authentication-overview.png)



\## 6. Conclusion



Wazuh successfully collected SSH authentication failure events from the Ubuntu target and generated related security alerts.



The test represents controlled authentication failures in a lab environment and does not establish that a real-world brute-force attack occurred.
