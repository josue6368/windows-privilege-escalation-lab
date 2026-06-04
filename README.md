# windows-privilege-escalation-lab
Performed Windows privilege escalation techniques in a controlled lab environment using PowerUp, PowerShell, and Windows service misconfiguration analysis. Identified vulnerable service configurations, validated privilege escalation paths from a low-privileged user context, and investigated activity through Sysmon and Splunk telemetry while documenting findings, detections, and remediation guidance.


---

# Windows Privilege Escalation and Detection Lab
## Project Overview
This lab demonstrates how a low-privileged Windows user can identify and exploit a misconfigured service to achieve a privilege escalation condition. The project combines offensive security techniques with defensive monitoring by using PowerUp for enumeration, Sysmon for endpoint telemetry, and Splunk for investigation and detection.

The objective was to simulate a real-world privilege escalation scenario, validate exploitability, and investigate the activity using security monitoring tools.

## Lab Objectives
* Identify Windows privilege escalation opportunities using PowerUp
* Enumerate service misconfigurations from a low-privileged user context
* Validate insecure file permissions on a LocalSystem service
* Demonstrate service binary modification by a low-privileged user
* Collect Sysmon telemetry during attacker activity
* Investigate privilege escalation activity in Splunk
* Document findings and remediation recommendations

## Lab Environment
### Infrastructure
| System                     | Purpose                          |
| -------------------------- | -------------------------------- |
| Windows 11                 | Target workstation               |
| Ubuntu Server 22.04        | Splunk Enterprise                |
| Ubuntu Server 22.04        | Zeek Network Security Monitor    |
| Sysmon                     | Endpoint telemetry collection    |
| Splunk Universal Forwarder | Log forwarding                   |
| PowerUp                    | Privilege escalation enumeration |

### Tools Used
* PowerUp
* PowerShell
* Sysmon
* Splunk Enterprise
* Splunk Universal Forwarder
* Windows Event Logs

## Attack Scenario
A low privileged user gains access to a Windows workstation and begins enumerating the host for privilege escalation opportunities.

Using PowerUp, the attacker identifies a vulnerable service running as LocalSystem with insecure permissions. The attacker validates write access to the service directory, modifies the service executable, and demonstrates the privilege escalation condition.

Sysmon records the attacker activity and Splunk is used to investigate the resulting telemetry.

## Phase 1: Vulnerable Service Creation
A vulnerable Windows service was created to simulate a common privilege escalation scenario involving weak permissions and an unquoted service path.

### Vulnerable Service Creation

<img width="1100" height="641" alt="Screenshot 2026-05-30 115232" src="https://github.com/user-attachments/assets/f51f4c22-e533-4a34-90bb-d03cce52a59f" />

*Figure 1. A vulnerable Windows service named VulnService was created to simulate a privilege escalation scenario. The service was configured to run as LocalSystem and was placed within a directory containing spaces in its path.* <br />

## Phase 2: Privilege Escalation Enumeration
PowerUp was downloaded and executed from the low-privileged user context to identify privilege escalation opportunities.

### PowerUp Identifies Unquoted Service Path Vulnerability

<img width="1714" height="754" alt="Screenshot 2026-05-30 123822" src="https://github.com/user-attachments/assets/b40727ae-5ca6-4e01-9cde-e39d370a9df5" />

*Figure 2. PowerUp identified VulnService as a vulnerable service due to an unquoted service path and weak file permissions. The service was running under the LocalSystem account, creating a potential privilege escalation path.* <br />


### Validation of Vulnerable Service Configuration

<img width="1427" height="314" alt="Screenshot 2026-05-30 124112" src="https://github.com/user-attachments/assets/e39c48b4-076c-4c2c-a05c-c258ff2f337d" />

*Figure 3. Service configuration details confirmed that VulnService was configured to run as LocalSystem and utilized an unquoted executable path located within Program Files.* <br />

### Low-Privilege User Context

<img width="1916" height="916" alt="Screenshot 2026-05-30 124537" src="https://github.com/user-attachments/assets/5fa9c2e3-9fd4-4d5a-87e0-ed52532e938f" />

*Figure 4. Enumeration activities were performed from the low-privileged user account (jsmith) to simulate an attacker operating without administrative privileges.* <br />

### Low-Privilege Enumeration with PowerUp

<img width="1943" height="1380" alt="Screenshot 2026-05-30 125906" src="https://github.com/user-attachments/assets/e8f802ab-bf3d-4345-8244-1068703e4d8e" />


*Figure 5. PowerUp was executed from the low-privileged user context and successfully identified multiple privilege escalation opportunities, including VulnService.* <br />

---

## Phase 3: Permission Validation

The identified service directory was examined to determine whether the low-privileged user possessed sufficient permissions to modify files associated with the service.

### Weak Service Directory Permissions

<img width="1564" height="553" alt="Screenshot 2026-05-30 130611" src="https://github.com/user-attachments/assets/8eeb29cb-b777-4b93-86d9-07b4f2ab059c" />



*Figure 6. The service directory permissions were intentionally weakened to simulate a misconfigured environment. Full Control permissions were granted to standard users, creating a privilege escalation opportunity.* <br />

### Low-Privilege User Access Verification 

<img width="1480" height="486" alt="Screenshot 2026-05-30 130720" src="https://github.com/user-attachments/assets/8959b99a-4fd1-4533-bf2e-605734598785" />




*Figure 7. The low-privileged user (jsmith) verified the permissions assigned to the VulnService directory and confirmed that standard users possessed Full Control access to files associated with the LocalSystem service.* <br />


### Service Directory Write Access Validation

<img width="1131" height="340" alt="Screenshot 2026-05-30 130751" src="https://github.com/user-attachments/assets/93dced9d-b268-476e-885b-d1b620636489" />

*Figure 8. The low-privileged user successfully created a file within the service directory, validating write access to a folder associated with a LocalSystem service.* <br />

---

## Phase 4: Service Binary Modification
The attacker validated the ability to modify files associated with the vulnerable service.

### Service Binary Modification Validation

<img width="1501" height="397" alt="Screenshot 2026-05-30 130940" src="https://github.com/user-attachments/assets/270e02d7-7fb8-40e4-a01e-1ab0df570f92" />

*Figure 9. The low-privileged user successfully created a backup copy of the service executable, demonstrating the ability to modify files associated with the LocalSystem service and validating conditions required for service binary replacement attacks.* <br />

### Baseline Service Binary Hash

<img width="1563" height="257" alt="Screenshot 2026-05-30 131244" src="https://github.com/user-attachments/assets/574303dd-2104-4871-af1c-86682133437a" />


*Figure 10. A SHA256 hash of the original VulnService executable was collected prior to modification. Baseline hashing supports integrity verification and enables analysts to identify unauthorized file changes during investigations.* <br />

### Service Binary Replacement Simulation

<img width="1332" height="126" alt="Screenshot 2026-05-30 131356" src="https://github.com/user-attachments/assets/e8159587-17f7-4634-9337-d982dd063532" />


*Figure 11. The low-privileged user successfully modified the executable associated with VulnService and verified the change by reading the file contents. This demonstrated the ability to alter files used by a LocalSystem service and validated the privilege escalation condition.* <br />


---

## Phase 5: Detection and Investigation

Sysmon telemetry was collected during attacker activity and investigated within Splunk.

### Sysmon Detection of Privilege Escalation Activity
Search Used

```
index=sysmon "Vuln Service"
```

<img width="2864" height="1832" alt="Screenshot 2026-05-30 131610" src="https://github.com/user-attachments/assets/dddfdaa4-c99d-4bd4-9b1e-d2e51cc43b10" />

*Figure 12. Sysmon captured process activity associated with the vulnerable service directory, including PowerShell and ICACLS commands executed by the low-privileged user. These events provide visibility into privilege escalation reconnaissance and file permission validation activities.* <br />

### Splunk Detection Summary

Search Used

```
index=sysmon "Vuln Service"
| stats count
```

<img width="1786" height="607" alt="Screenshot 2026-05-30 132451" src="https://github.com/user-attachments/assets/f4dd37a7-16ec-4427-80de-f24a4213ea24" />


*Figure 13. Splunk identified nine Sysmon events associated with activity targeting the vulnerable service directory, confirming visibility into privilege escalation reconnaissance and file permission validation activity.* <br />


---

## Findings 
The following conditions enabled the privilege escalation scenario:

* LocalSystem service execution context
* Unquoted service path
* Weak file permissions
* User-writable service directory
* Ability to modify files associated with a privileged service

These conditions allowed a low-privileged user to modify files associated with a service running as LocalSystem.


## MITRE ATT&CK Mapping

| Technique | Description                                      |
| --------- | ------------------------------------------------ |
| T1574.009 | Path Interception by Unquoted Path               |
| T1574.010 | Services File Permissions Weakness               |
| T1543.003 | Create or Modify System Process: Windows Service |
| T1059.001 | PowerShell                                       |
| T1083     | File and Directory Discovery                     |
| T1036     | Masquerading                                     |


## Detection Opportunities
Organizations can detect similar activity by monitoring:

* PowerShell execution
* Service configuration modifications
* ICACLS usage
* Service executable changes
* File integrity monitoring alerts
* Sysmon Event ID 1 (Process Creation)
* Sysmon Event ID 7 (Image Load)
* Windows Service Control Manager events

## Remediation Recommendations

* Quote all Windows service paths containing spaces.
* Restrict write permissions on service directories.
* Implement file integrity monitoring.
* Review service permissions regularly.
* Monitor for suspicious PowerShell execution.
* Enable Sysmon endpoint logging.
* Forward endpoint telemetry to a SIEM platform.

## Key Skills Demonstrated 

* Windows Privilege Escalation
* PowerUp Enumeration
* Windows Service Security
* Access Control Analysis
* PowerShell
* Sysmon Monitoring
* Splunk Investigation
* Endpoint Detection
* Threat Hunting
* Security Log Analysis
* MITRE ATT&CK Mapping
* Incident Documentation


## Conclusion

This lab demonstrated how insecure Windows service configurations can create privilege escalation opportunities for low-privileged users. Using PowerUp, Sysmon, and Splunk, the attack path was identified, validated, and investigated from both offensive and defensive perspectives. The project highlights the importance of secure service configurations, endpoint visibility, and effective monitoring practices in modern enterprise environments.\


### Author
:floppy_disk: josue6368 <br/>
Cybersecurity Analyst | IT Professional





















