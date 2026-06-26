# Adversary Emulation & Detection Engineering Lab

**Course:** Information Security and Cryptography (CSE-802)

**University:** University of Dhaka

**Student Name:** Md Abdul Mazed

**Roll:** SH-60038

**Registration No:** H-430

**Batch:** 6

**Teaching Assistant:** Mukul Ahmed

---

# Project Overview

This project presents the implementation of a Security Operations Center (SOC) Home Lab for adversary emulation and detection engineering. The objective of the lab is to simulate real-world cyber attacks, collect endpoint telemetry, and detect malicious activities using behavior-based analytics.

The lab environment consists of two virtual machines: a Windows Server 2019 machine acting as the victim endpoint and a Kali Linux machine hosting Splunk Enterprise as the Security Information and Event Management (SIEM) platform. Microsoft Sysmon is deployed on the Windows Server to capture detailed system events, while the Splunk Universal Forwarder sends these logs to Splunk for centralized monitoring and analysis.

To emulate realistic attacker behavior, the Atomic Red Team framework is used to execute selected MITRE ATT&CK techniques, including Scheduled Task creation, MSHTA execution, LSASS credential dumping, PowerShell download cradle execution, and Registry modification. Detection is performed using Sysmon Event IDs and Splunk Search Processing Language (SPL) queries.

---

# Table of Contents

* Introduction

  * Background
  * Aim and Objectives
  * Scope of the Lab
* Lab Environment
* Network Topology
* System Architecture
* Tools and Technologies
* Splunk Enterprise Installation
* Sysmon Installation
* Splunk Universal Forwarder Configuration
* Data Collection Configuration
* Log Verification
* Atomic Red Team Installation
* Attack Simulation
* Detection Engineering
* SPL Detection Queries
* Results and Discussion
* Sysmon Event Summary
* Conclusion
* References

---

# 1. Introduction

## 1.1 Background

Cyber threats are becoming increasingly sophisticated, making it essential for organizations to deploy effective monitoring and detection solutions. Security Operations Centers (SOCs) play a vital role in identifying malicious activities, analyzing security events, and responding to cyber incidents.

Building a SOC home lab provides a practical environment for understanding how attacks are performed and how they can be detected using modern security technologies.

This project focuses on designing and implementing a virtual SOC environment to simulate adversary techniques and evaluate detection capabilities.

The lab was built using two virtual machines:

* Windows Server 2019 (Victim Machine)
* Kali Linux (Splunk SIEM Server)

Microsoft Sysmon was installed on Windows Server to capture endpoint telemetry, while the Splunk Universal Forwarder transmitted those logs to Splunk Enterprise for centralized analysis.

Atomic Red Team was then used to simulate multiple MITRE ATT&CK techniques to generate realistic attack telemetry.

Instead of relying on signature-based detection, this project demonstrates behavior-based threat hunting using Sysmon Event IDs and Splunk Search Processing Language (SPL).

---

## 1.2 Aim and Objectives

### Aim

The aim of this project is to build a Security Operations Center (SOC) Home Lab capable of simulating cyber attacks and detecting them through centralized log collection and behavior-based analytics.

### Objectives

* Build an isolated SOC lab using Oracle VirtualBox.
* Deploy Windows Server 2019 and Kali Linux virtual machines.
* Install Splunk Enterprise.
* Configure Splunk Universal Forwarder.
* Install Microsoft Sysmon.
* Generate endpoint telemetry.
* Execute Atomic Red Team attack simulations.
* Detect attack behaviors using Splunk SPL queries.
* Map detections with the MITRE ATT&CK Framework.

---

## 1.3 Scope of the Lab

The scope of this project includes:

* Deployment of a virtual SOC environment
* Windows endpoint monitoring
* SIEM implementation using Splunk Enterprise
* Endpoint telemetry collection using Sysmon
* Threat emulation using Atomic Red Team
* Detection Engineering
* Threat Hunting
* Log Analysis
* MITRE ATT&CK Mapping

The project demonstrates the complete workflow of adversary emulation, telemetry collection, centralized log management, and security monitoring.

---

# 2. Lab Environment

The entire laboratory environment was created using Oracle VirtualBox.

Two virtual machines were deployed.

| Virtual Machine     | Purpose            |
| ------------------- | ------------------ |
| Windows Server 2019 | Victim Endpoint    |
| Kali Linux          | Splunk SIEM Server |

The Windows Server generated telemetry using Microsoft Sysmon.

Splunk Universal Forwarder continuously forwarded Windows logs to the Kali Linux SIEM.

Splunk Enterprise indexed the logs and allowed analysts to investigate attack activities through SPL searches.

---

# 2.1 Network Topology

Both virtual machines were connected to the same VirtualBox NAT Network.

```
                 +----------------------+
                 |   Kali Linux VM      |
                 |----------------------|
                 | Splunk Enterprise    |
                 | Port 8000            |
                 | Port 9997            |
                 +----------+-----------+
                            ^
                            |
            Splunk Universal Forwarder
                            |
                            v
                 +----------------------+
                 | Windows Server 2019  |
                 |----------------------|
                 | Sysmon               |
                 | Atomic Red Team      |
                 +----------------------+
```

The Windows Server forwarded Sysmon logs to Splunk over port **9997**, while the Splunk Web interface was accessible through port **8000**.

---

# 2.2 System Architecture

The monitoring workflow followed these steps:

1. Atomic Red Team executes attack techniques.
2. Sysmon records endpoint events.
3. Splunk Universal Forwarder forwards logs.
4. Splunk Enterprise indexes the telemetry.
5. Analysts perform detection using SPL queries.
6. Attack behaviors are mapped to the MITRE ATT&CK Framework.

This architecture demonstrates an end-to-end SOC monitoring and detection workflow in an isolated lab environment.
---

# 3. Tools and Technologies

The following tools and technologies were used to build the SOC Home Lab.

| Tool                       | Purpose                 |
| -------------------------- | ----------------------- |
| Oracle VirtualBox          | Virtualization Platform |
| Kali Linux                 | Splunk SIEM Server      |
| Windows Server 2019        | Victim Machine          |
| Splunk Enterprise          | SIEM Platform           |
| Splunk Universal Forwarder | Log Forwarding          |
| Microsoft Sysmon           | Endpoint Monitoring     |
| Atomic Red Team            | Attack Simulation       |
| PowerShell                 | Attack Execution        |

---

## 3.1 Oracle VirtualBox

Oracle VirtualBox was used as the virtualization platform to create an isolated SOC environment. Two virtual machines were deployed within the same NAT Network to allow secure communication between the victim endpoint and the SIEM server.

**Role**

* Hardware Virtualization
* Network Isolation
* Resource Management

---

## 3.2 Kali Linux

Kali Linux acted as the SIEM server.

Splunk Enterprise was installed on Kali Linux to receive Windows telemetry, index log data, and perform threat hunting using SPL queries.

**Role**

* Splunk Enterprise Server
* Log Collection
* Search and Analysis

---

## 3.3 Windows Server 2019

Windows Server 2019 served as the victim endpoint.

All Atomic Red Team simulations were executed on this machine.

Sysmon collected endpoint telemetry, and Splunk Universal Forwarder transmitted the logs to Splunk Enterprise.

**Role**

* Victim Endpoint
* Attack Simulation
* Log Generation

---

## 3.4 Splunk Enterprise

Splunk Enterprise served as the Security Information and Event Management (SIEM) platform.

Its responsibilities included:

* Receiving Windows Logs
* Indexing Events
* Running SPL Queries
* Threat Hunting
* Dashboard Visualization

### Installation

```bash
cd ~/Downloads

sudo tar xvzf splunk-*-linux-amd64.tgz -C /opt

sudo /opt/splunk/bin/splunk start --accept-license

sudo /opt/splunk/bin/splunk enable boot-start -user root
```

Enable receiving port

```bash
sudo /opt/splunk/bin/splunk enable listen 9997
```

Create Index

```bash
sudo /opt/splunk/bin/splunk add index win
```

Splunk Web Interface

```
http://192.168.11.131:8000
```

---

## 3.5 Splunk Universal Forwarder

Splunk Universal Forwarder was installed on Windows Server 2019.

Its primary responsibility was forwarding Windows Event Logs to Splunk Enterprise.

### Configuration

```powershell
cd "C:\Program Files\SplunkUniversalForwarder\bin"

.\splunk.exe add forward-server 192.168.11.131:9997
```

Forwarder Service

```
SplunkForwarder
```

---

## 3.6 Microsoft Sysmon

Microsoft Sysmon was used to generate detailed endpoint telemetry.

Sysmon records:

* Process Creation
* Registry Modification
* Network Connections
* Process Access
* File Creation
* Driver Loading

### Installation

```powershell
cd C:\Lab\Sysmon

.\sysmon64.exe -accepteula -i sysmonconfig.xml
```

Verify Installation

```powershell
Get-Service Sysmon64
```

Sysmon Event Log

```
Microsoft-Windows-Sysmon/Operational
```

---

## 3.7 Atomic Red Team

Atomic Red Team was used to simulate attacker behavior based on the MITRE ATT&CK Framework.

It generates realistic endpoint activity that can be detected using SIEM.

---

# 4. Lab Setup and Configuration

The lab environment consists of:

* Windows Server 2019
* Kali Linux
* Splunk Enterprise
* Sysmon
* Universal Forwarder

The objective is to collect endpoint telemetry from Windows and analyze it in Splunk.

---

# 4.1 Installing Splunk Enterprise

Splunk Enterprise was installed on the Kali Linux virtual machine.

### Commands

```bash
cd ~/Downloads

sudo tar xvzf splunk-*-linux-amd64.tgz -C /opt

sudo /opt/splunk/bin/splunk start --accept-license

sudo /opt/splunk/bin/splunk enable boot-start -user root
```

Enable Receiver

```bash
sudo /opt/splunk/bin/splunk enable listen 9997
```

Create Index

```bash
sudo /opt/splunk/bin/splunk add index win
```

---

# 4.2 Installing Sysmon

Sysmon was installed on Windows Server 2019.

Commands

```powershell
cd C:\Lab\Sysmon

.\sysmon64.exe -accepteula -i sysmonconfig.xml
```

Verify

```powershell
Get-Service Sysmon64
```

---

# 4.3 Configuring Splunk Universal Forwarder

After installation, the Universal Forwarder was configured to send telemetry to Splunk.

Configuration Command

```powershell
cd "C:\Program Files\SplunkUniversalForwarder\bin"

.\splunk.exe add forward-server 192.168.11.131:9997
```

---

# 4.4 Configuring Data Collection

The **inputs.conf** file was configured as follows.

```ini
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = win
renderXml = true

[WinEventLog://Microsoft-Windows-PowerShell/Operational]
disabled = 0
index = win
renderXml = true

[WinEventLog://Security]
disabled = 0
index = win
```

Restart Forwarder

```powershell
.\splunk.exe restart
```

---

# 4.5 Verifying Log Collection

The following SPL queries were executed to verify successful telemetry collection.

### Verify Hosts

```spl
index=win
| stats count by host,sourcetype
```

### Verify Event IDs

```spl
index=win
| stats count by EventCode
```

### Verify Process Creation

```spl
index=win EventCode=1
| table _time Computer Image CommandLine
```

The successful appearance of Windows Event Logs in Splunk confirmed that:

* Sysmon was generating telemetry.
* Universal Forwarder was forwarding logs.
* Splunk Enterprise was successfully indexing the events.
---

# 5. Atomic Red Team Installation

Atomic Red Team is an open-source adversary emulation framework that provides small, focused tests mapped to the MITRE ATT&CK framework. It allows security analysts to validate detections by safely simulating common attacker techniques.

## 5.1 Installing Invoke-AtomicRedTeam

Open **Windows PowerShell** as **Administrator** and execute the following commands:

```powershell
Set-ExecutionPolicy Bypass -Scope CurrentUser -Force

IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)

Install-AtomicRedTeam -getAtomics -Force

Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force
```

---

## 5.2 PowerShell Configuration

To allow execution of Atomic Red Team tests:

```powershell
Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
```

Verify installation:

```powershell
Get-Module Invoke-AtomicRedTeam -ListAvailable
```

---

## 5.3 Windows Defender Exclusion

Some Atomic Red Team payloads are detected by Windows Defender. For laboratory testing only, an exclusion path was added.

```powershell
Add-MpPreference -ExclusionPath "C:\AtomicRedTeam"
```

> **Note:** This configuration was used only in the isolated laboratory environment.

---

# 6. Attack Simulation (Red Team)

The following MITRE ATT&CK techniques were executed to generate attack telemetry.

---

## 6.1 T1053.005 – Scheduled Task (Persistence)

### Description

This technique creates a scheduled task that automatically executes malicious code to maintain persistence after system reboot.

### Show Test Information

```powershell
Invoke-AtomicTest T1053.005 -ShowDetailsBrief
```

### Execute Test

```powershell
Invoke-AtomicTest T1053.005 -TestNumbers 1
```

### Expected Result

* Creates a scheduled task
* Generates Sysmon Event ID 1
* Detectable through `schtasks.exe`

---

## 6.2 T1218.005 – MSHTA (Defense Evasion)

### Description

MSHTA executes HTA applications and JavaScript payloads to bypass application control mechanisms.

### Show Test

```powershell
Invoke-AtomicTest T1218.005 -ShowDetailsBrief
```

### Execute Test

```powershell
Invoke-AtomicTest T1218.005 -TestNumbers 1
```

### Expected Result

* Launches `mshta.exe`
* Executes HTA or JavaScript payload
* Generates Sysmon Event ID 1

---

## 6.3 T1003.001 – LSASS Memory Dumping (Credential Access)

### Description

This technique attempts to dump the memory of the LSASS process to obtain user credentials.

### Download Prerequisites

```powershell
Invoke-AtomicTest T1003.001 -TestNumbers 1 -GetPrereqs
```

### Execute Test

```powershell
Invoke-AtomicTest T1003.001 -TestNumbers 1
```

### Expected Result

* ProcDump accesses `lsass.exe`
* Generates Sysmon Event ID 10
* Records suspicious GrantedAccess values

---

## 6.4 T1059.001 – PowerShell Download Cradle (Execution)

### Description

PowerShell downloads and executes a remote script directly in memory.

### Show Test

```powershell
Invoke-AtomicTest T1059.001 -ShowDetailsBrief
```

### Execute Test

```powershell
Invoke-AtomicTest T1059.001 -TestNumbers 1
```

### Expected Result

* PowerShell launches
* Remote script downloaded
* Generates Sysmon Event ID 1
* Generates PowerShell Event ID 4104

---

## 6.5 T1112 – Registry Modification (Defense Evasion)

### Description

Registry values are modified to disable security controls or establish persistence.

### Show Test

```powershell
Invoke-AtomicTest T1112 -ShowDetailsBrief
```

### Execute Test

```powershell
Invoke-AtomicTest T1112 -TestNumbers 3,5
```

### Expected Result

* Registry values modified
* Windows Defender settings changed
* Trusted Sites entries created
* Generates Sysmon Event ID 13

---

# Cleanup After Testing

After completing all attack simulations, cleanup commands were executed to restore the environment.

```powershell
Invoke-AtomicTest T1053.005,T1218.005,T1003.001,T1059.001,T1112 -Cleanup
```

---

# Attack Summary

| MITRE ID  | Technique                  | Tactic            | Primary Detection                   |
| --------- | -------------------------- | ----------------- | ----------------------------------- |
| T1053.005 | Scheduled Task             | Persistence       | Sysmon Event ID 1                   |
| T1218.005 | MSHTA                      | Defense Evasion   | Sysmon Event ID 1                   |
| T1003.001 | LSASS Dumping              | Credential Access | Sysmon Event ID 10                  |
| T1059.001 | PowerShell Download Cradle | Execution         | Sysmon Event ID 1 + PowerShell 4104 |
| T1112     | Registry Modification      | Defense Evasion   | Sysmon Event ID 13                  |

---

# Attack Workflow

```text
Atomic Red Team
        │
        ▼
Windows Server 2019
        │
        ▼
Microsoft Sysmon
        │
        ▼
Splunk Universal Forwarder
        │
        ▼
Splunk Enterprise
        │
        ▼
Threat Detection using SPL Queries
```
---

# 7. Detection and Analysis (Blue Team)

After executing the Atomic Red Team tests, all generated telemetry was forwarded to Splunk Enterprise through the Splunk Universal Forwarder. Detection was performed using behavior-based Search Processing Language (SPL) queries rather than searching for specific tool names.

The Splunk Add-on for Microsoft Sysmon parsed the raw XML logs into searchable fields such as:

* Image
* CommandLine
* ParentImage
* TargetImage
* TargetObject
* GrantedAccess

These fields enabled effective detection of malicious behaviors.

---

## 7.1 Attack Overview

Before analyzing each attack individually, the following query was executed to verify that the attack tools had been captured by Sysmon.

```spl
index=win EventCode=1
(Image="*schtasks.exe" OR
 Image="*mshta.exe" OR
 Image="*powershell.exe" OR
 Image="*reg.exe" OR
 Image="*cmd.exe")
| stats count by Image
```

This query confirmed that all attack-related processes were successfully logged.

---

# 7.2 Scheduled Task Detection (T1053.005)

### Detection Logic

Detects creation of scheduled tasks using **schtasks.exe**.

### SPL Query

```spl
index=win source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
EventCode=1
Image="*\\schtasks.exe"
CommandLine="*/create*"
| table _time, Computer, User, Image, CommandLine, ParentImage
```

### Detection Result

* Process: schtasks.exe
* Command Line captured
* Parent Process identified
* Persistence activity detected

### Most Useful Event ID

**Sysmon Event ID 1 – Process Creation**

---

# 7.3 MSHTA Detection (T1218.005)

### Detection Logic

Detects execution of **mshta.exe** used to bypass application controls.

### SPL Query

```spl
index=win source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
EventCode=1
Image="*\\mshta.exe"
| table _time, Computer, User, Image, CommandLine, ParentImage
```

### Detection Result

* mshta.exe executed
* Parent Process identified
* HTA payload visible
* Defense Evasion detected

### Most Useful Event ID

**Sysmon Event ID 1**

---

# 7.4 LSASS Dump Detection (T1003.001)

### Detection Logic

Detects suspicious access to **lsass.exe**.

### SPL Query

```spl
index=win source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
EventCode=10
TargetImage="*\\lsass.exe"
| table _time, Computer, SourceImage, TargetImage, GrantedAccess, CallTrace
```

### Detection Result

* Source Process identified
* Target Process identified
* GrantedAccess values recorded
* Credential Dumping detected

### Most Useful Event ID

**Sysmon Event ID 10 – Process Access**

---

# 7.5 PowerShell Download Cradle Detection (T1059.001)

### Detection Logic

Detects PowerShell downloading and executing remote scripts.

### SPL Query

```spl
index=win source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
EventCode=1
Image="*\\powershell.exe"
(CommandLine="*DownloadString*" OR
CommandLine="*Net.WebClient*" OR
CommandLine="*Invoke-WebRequest*" OR
CommandLine="*IEX*")
| table _time, Computer, User, Image, CommandLine, ParentImage
```

---

### Alternative PowerShell Detection

```spl
index=win
source="XmlWinEventLog:Microsoft-Windows-PowerShell/Operational"
EventCode=4104
(ScriptBlockText="*DownloadString*" OR
 ScriptBlockText="*Net.WebClient*" OR
 ScriptBlockText="*IEX*")
| table _time, Computer, ScriptBlockText
```

### Detection Result

* PowerShell execution detected
* DownloadString observed
* IEX observed
* Remote payload execution detected

### Most Useful Event IDs

* Sysmon Event ID 1
* PowerShell Event ID 4104

---

# 7.6 Registry Modification Detection (T1112)

### Detection Logic

Detects suspicious registry modifications affecting Windows Defender, Trusted Sites, and credential storage.

### SPL Query

```spl
index=win source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
EventCode=13
(TargetObject="*DisableAntiSpyware*" OR
 TargetObject="*DisableRealtimeMonitoring*" OR
 TargetObject="*ZoneMap*" OR
 TargetObject="*Winlogon*" OR
 TargetObject="*\\Windows Defender\\*")
| table _time, Computer, Image, TargetObject, Details
```

### Detection Result

* Registry path captured
* Registry values captured
* Image identified
* Defense Evasion detected

### Most Useful Event ID

**Sysmon Event ID 13 – Registry Value Set**

---

# 8. Detection Queries Summary

| Technique      | Detection Query      | Event ID          |
| -------------- | -------------------- | ----------------- |
| Scheduled Task | schtasks.exe         | Event ID 1        |
| MSHTA          | mshta.exe            | Event ID 1        |
| LSASS Dump     | lsass.exe            | Event ID 10       |
| PowerShell     | DownloadString / IEX | Event ID 1 & 4104 |
| Registry       | TargetObject         | Event ID 13       |

---

# MITRE ATT&CK Mapping

| Technique ID | Technique                  | Tactic            | Detection Source                  |
| ------------ | -------------------------- | ----------------- | --------------------------------- |
| T1053.005    | Scheduled Task             | Persistence       | Sysmon Event ID 1                 |
| T1218.005    | MSHTA                      | Defense Evasion   | Sysmon Event ID 1                 |
| T1003.001    | LSASS Dump                 | Credential Access | Sysmon Event ID 10                |
| T1059.001    | PowerShell Download Cradle | Execution         | Sysmon Event ID 1 + Event ID 4104 |
| T1112        | Registry Modification      | Defense Evasion   | Sysmon Event ID 13                |

---

# Detection Workflow

```text
Attack Simulation
        │
        ▼
Sysmon Telemetry
        │
        ▼
Universal Forwarder
        │
        ▼
Splunk Enterprise
        │
        ▼
SPL Detection Query
        │
        ▼
Threat Detection
        │
        ▼
MITRE ATT&CK Mapping
```

---

# Key Detection Capabilities

✔ Process Creation Monitoring

✔ Credential Dump Detection

✔ Registry Monitoring

✔ PowerShell Monitoring

✔ Threat Hunting

✔ Behavior-Based Detection

✔ MITRE ATT&CK Mapping

✔ Endpoint Visibility
