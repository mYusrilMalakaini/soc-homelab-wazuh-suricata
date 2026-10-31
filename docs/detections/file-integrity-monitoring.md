\# File Integrity Monitoring (FIM)



\## 1. Objective



Validate Wazuh File Integrity Monitoring (FIM) by creating and modifying test files in a monitored endpoint directory.



\## 2. Lab Configuration



| Component | Details |

|---|---|

| Endpoint | Ubuntu Server (`192.168.77.20`) |

| Wazuh Agent | `jh-server` |

| SIEM | Wazuh 4.14.9 |

| Test directory | `/home/jadihacker` |



\## 3. Test Procedure



Two controlled file operations were performed on the Ubuntu endpoint:



1\. A new test file was created in the monitored directory.

2\. The same test file was modified to change its contents.



Wazuh FIM monitored the file activity and generated corresponding security events.



\## 4. Detection Results



| Wazuh Rule ID | Detection |

|---|---|

| `554` | File added to the system |

| `550` | File integrity checksum changed |



\## 5. Evidence



\### File Creation Detection



!\[FIM file added](../../screenshots/wazuh/03-fim-file-added-554.png)



\### File Modification Detection



!\[FIM file modified](../../screenshots/wazuh/04-fim-file-modified-550.png)



\## 6. Conclusion



Wazuh successfully generated FIM alerts when a test file was created and subsequently modified in the monitored endpoint directory.



These results validate basic file creation and integrity-change monitoring. Exact file-path attribution and Who-data attribution require separate validation.
