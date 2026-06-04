# windows-privilege-escalation-lab
Performed Windows privilege escalation techniques in a controlled lab environment using PowerShell, winPEAS, and Windows misconfiguration analysis. Investigated privilege escalation opportunities, validated detection visibility through Sysmon and Splunk telemetry, and documented findings and remediation guidance.


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

<img width="1100" height="641" alt="Screenshot 2026-05-30 115232" src="https://github.com/user-attachments/assets/f51f4c22-e533-4a34-90bb-d03cce52a59f" />

*Figure 1. A vulnerable Windows service named VulnService was created to simulate a privilege escalation scenario. The service was configured to run as LocalSystem and was placed within a directory containing spaces in its path.* <br />

## Phase 2: Privilege Escalation Enumeration
PowerUp was downloaded and executed from the low-privileged user context to identify privilege escalation opportunities.

<img width="1714" height="754" alt="Screenshot 2026-05-30 123822" src="https://github.com/user-attachments/assets/b40727ae-5ca6-4e01-9cde-e39d370a9df5" />

*Figure 2. PowerUp identified VulnService as a vulnerable service due to an unquoted service path and weak file permissions. The service was running under the LocalSystem account, creating a potential privilege escalation path.* <br />

<img width="1427" height="314" alt="Screenshot 2026-05-30 124112" src="https://github.com/user-attachments/assets/e39c48b4-076c-4c2c-a05c-c258ff2f337d" />

*Figure 3. Service configuration details confirmed that VulnService was configured to run as LocalSystem and utilized an unquoted executable path located within Program Files.* <br />

<img width="1916" height="916" alt="Screenshot 2026-05-30 124537" src="https://github.com/user-attachments/assets/5fa9c2e3-9fd4-4d5a-87e0-ed52532e938f" />

*Figure 4. Enumeration activities were performed from the low-privileged user account (jsmith) to simulate an attacker operating without administrative privileges.* <br />

<img width="1943" height="1380" alt="Screenshot 2026-05-30 125906" src="https://github.com/user-attachments/assets/e8f802ab-bf3d-4345-8244-1068703e4d8e" />


*Figure 5. PowerUp was executed from the low-privileged user context and successfully identified multiple privilege escalation opportunities, including VulnService.* <br />

---

## Phase 3: Permission Validation

The identified service directory was examined to determine whether the low-privileged user possessed sufficient permissions to modify files associated with the service.

<img width="1480" height="486" alt="Screenshot 2026-05-30 130720" src="https://github.com/user-attachments/assets/a1f5705d-612b-409d-8b12-daf49e75294b" />


*Figure 6. The service directory permissions were modified to simulate an insecure configuration that granted Full Control permissions to standard users.* <br />








*Figure 7. The low-privileged user account successfully verified access to the service directory and confirmed the ability to interact with files associated with the LocalSystem service.* <br />

<img width="1131" height="340" alt="Screenshot 2026-05-30 130751" src="https://github.com/user-attachments/assets/93dced9d-b268-476e-885b-d1b620636489" />

*Figure 8. The low-privileged user successfully created a file within the VulnService directory. This validated write access to a directory associated with a LocalSystem service and demonstrated the ability to modify service-related files.* <br />

---

## Phase 4: Service Binary Modification
The attacker validated the ability to modify files associated with the vulnerable service.


<img width="1501" height="397" alt="Screenshot 2026-05-30 130940" src="https://github.com/user-attachments/assets/270e02d7-7fb8-40e4-a01e-1ab0df570f92" />

*Figure 9. The low-privileged user successfully created a backup copy of the service executable, demonstrating the ability to modify files associated with the LocalSystem service and validating conditions required for service binary replacement attacks.* <br />
















