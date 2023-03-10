# M365 Defender Custom Playbooks
M365 Defender SOC Playbooks - "**Ransomware Recovery “SOC – Automated Restore Playbook**"


A power automate playbook that integrate as connected application to Microsoft Defender for Endpoint and OneDrive for business to allow SOC to recover from ransomware files/data disruption via an automated process. Noting that the playbook trigger can be replaced with a recurrence schedule instead of MDE alert base.


Pre-reqs:
- Microsoft Defender for Endpoint.
- Concerned machines are already conbaored and synced with OneDrive For business.
- POwer Automate / Azure Logic Apps.

Please note this is just a sample - PoC. Below are the main configuration & setup steps:

First you need to create app registeration under your M365 Defender tenant, make sure to consent the folowing application permissions under "Microsoft Graph" API permissions:

<img width="466" alt="OneDrive App REg MDE permissoon" src="https://user-images.githubusercontent.com/39443323/213397388-df950740-d706-47d8-a739-3b94e74dec8d.png">

Then follow the following steps to import the Power automate playbook, you can alos leverge the same Power automate playbook steps to be used in an Azure Logic App:

1. Attached the Power Automate playbook, you can import it and connect as MDE application for more details please check https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/api-microsoft-flow?view=o365-worldwide
2. Ensure of adding the following ransomware activites as MDE custom detection rules in addition to https://learn.microsoft.com/en-us/microsoft-365/security/defender/advanced-hunting-find-ransomware?view=o365-worldwide: (you might consolidate all activites into one custom detection rule as well):
- Open http://security.microsoft.com portal
- Open Advanced hunting blade, copy and paste the below detection KQL query and validate if there is no any syntax errors:

union DeviceFileEvents, DeviceProcessEvents
| where ActionType == "FileCreated" and InitiatingProcessFileName contains "python" and InitiatingProcessCommandLine contains "encrypt"
| project Timestamp, ReportId, DeviceId, DeviceName, ActionType,FileName, FolderPath, SHA1, SHA256, MD5, FileSize, ProcessId, ProcessCommandLine,ProcessTokenElevation, ProcessCreationTime

- Then save the query as custom detection rule, you might use the following attributes value as a sample:

> Detection Name: Ransomware - python encryption script interpreter process was created by powershell.exe
> Frequency: you can define it
> Alert title: Ransomware - python encryption script interpreter process was created by powershell
> Severity: define it
> Category: Ransomware
> Description: Adversaries may abuse command and script interpreters to execute commands, scripts, or binaries. These interfaces and languages provide ways of interacting with computer systems and are a common feature across many different platforms.
T1059.006: Python and T1059: Command and Scripting Interpreter
> Recommended actions: Investigate and check the encrypted files and confirm if it's malicious and ransom activity then use native OneDrive Defender recovery feature.

![MDEOneDriveRansomwareRecovery-part2-New](https://user-images.githubusercontent.com/39443323/213176839-8b35b676-ab10-44db-ae78-31aeaf6a2082.gif)
