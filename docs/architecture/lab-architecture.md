\# SOC Homelab Architecture



\## 1. Overview



This project uses an isolated virtual lab built with Oracle VirtualBox to simulate a small Security Operations Center (SOC) environment.



The lab combines Wazuh as the Security Information and Event Management (SIEM) platform and Suricata as the Network Intrusion Detection System (IDS).



\## 2. Lab Components



| Virtual Machine | Role | Internal IP |

|---|---|---|

| Wazuh Server | SIEM, event analysis, and alert management | `192.168.77.10` |

| Target Server | Ubuntu endpoint running Wazuh Agent and Suricata | `192.168.77.20` |

| Kali Linux | Controlled security testing machine | `192.168.77.30` |



\*\*Internal network:\*\* `192.168.77.0/24`



\*\*VirtualBox network mode:\*\* Internal Network (`intnet`)



\## 3. Component Responsibilities



\### Wazuh Server



\- Receives security events from the monitored endpoint.

\- Processes events using decoders and detection rules.

\- Generates alerts for security monitoring and investigation.

\- Provides the Wazuh Dashboard for reviewing alerts.



\### Target Server



\- Runs the Wazuh Agent to forward monitored logs.

\- Runs Suricata to inspect network traffic.

\- Provides endpoint authentication and system logs.

\- Stores Suricata events in `/var/log/suricata/eve.json`.



\### Kali Linux



\- Acts as the controlled testing machine.

\- Generates SSH authentication failure tests.

\- Performs network reconnaissance tests against the target.



\## 4. Event Collection and Detection Flow



1\. Kali Linux generates controlled test traffic toward the Ubuntu target.

2\. The Target Server produces system and security events.

3\. Suricata inspects network traffic and writes IDS events to `eve.json`.

4\. Wazuh Agent collects configured endpoint and Suricata logs.

5\. Wazuh Manager processes the incoming events using its decoders and rules.

6\. Alerts are reviewed through the Wazuh Dashboard.



\## 5. Network Isolation



The three virtual machines communicate through the VirtualBox Internal Network named `intnet`.



This network provides an isolated environment for controlled security testing. Testing should remain limited to systems owned by the lab operator or systems for which explicit authorization has been granted.



\## 6. Architecture Scope



The current lab validates endpoint authentication monitoring, file integrity monitoring, and Suricata-to-Wazuh network alert integration.



Additional capabilities, including malware detection validation, Who-data attribution, and Active Response, require separate testing before they can be considered completed.
