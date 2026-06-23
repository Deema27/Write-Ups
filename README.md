# Cybersecurity Lab Reports

This repository contains a collection of hands-on cybersecurity lab reports documenting reconnaissance, vulnerability identification, exploitation, post-exploitation activities, and flag retrieval across multiple target environments.

## Overview

The labs focus on practical offensive security techniques and vulnerability assessment methodologies using industry-standard tools and frameworks. Each report documents the full attack lifecycle, including service enumeration, vulnerability discovery, exploitation, and validation of successful access.

## Skills Demonstrated

* Network Reconnaissance
* Service Enumeration
* Vulnerability Assessment
* Exploit Research
* SMB Enumeration
* FTP Enumeration
* Web Application Exploitation
* Metasploit Framework
* Remote Access Techniques
* Post-Exploitation Enumeration
* Windows and Linux System Navigation
* Credential Discovery and Validation
* Capture the Flag (CTF) Methodology

## Tools Used

* Nmap
* Metasploit Framework
* CrackMapExec
* SMBClient
* SMBMap
* Evil-WinRM
* FTP
* Windows Command Line
* Linux Command Line

## Lab Reports

### Lab 1 — Authentication and Access Control Weaknesses

This lab focused on identifying and exploiting weak authentication mechanisms.

**Key Findings**

* Anonymous FTP access allowed retrieval of sensitive files.
* Weak SMB authentication exposed administrative access.
* Misconfigured permissions enabled unauthorized access to flag files.

**Techniques**

* FTP Enumeration
* Anonymous Authentication Testing
* SMB Enumeration
* Credential Validation

---

### Lab 2 — Public Vulnerability Exploitation

This lab focused on exploiting known vulnerabilities in outdated software and operating systems.

**Key Findings**

* Apache HTTP Server 2.4.49 was vulnerable to CVE-2021-41773.
* Windows 7 SMB service was vulnerable to MS17-010 (EternalBlue).
* Successful remote code execution was achieved on both targets.

**Techniques**

* Vulnerability Identification
* Metasploit Exploitation
* Reverse Shell Access
* Post-Exploitation Enumeration

**Vulnerabilities**

* CVE-2021-41773
* MS17-010 (EternalBlue)

---

### Lab 3 — SMB Enumeration and Credential Exposure

This lab focused on identifying exposed SMB resources and leveraging credential disclosure.

**Key Findings**

* Anonymous SMB access exposed sensitive configuration files.
* Plaintext credentials were recovered from a configuration file.
* Remote PowerShell access was obtained using Evil-WinRM.

**Techniques**

* SMB Share Enumeration
* Configuration File Analysis
* Credential Validation
* Remote Management Access

---

## Learning Outcomes

Through these labs, the following concepts were reinforced:

* Identifying exposed network services
* Mapping attack surfaces
* Exploiting common misconfigurations
* Leveraging publicly known vulnerabilities
* Enumerating Windows and Linux systems
* Validating and abusing exposed credentials
* Performing post-exploitation discovery
* Documenting findings in a professional format

## Disclaimer

These activities were performed in controlled lab and educational environments for learning and research purposes only. The techniques documented in this repository should only be used on systems for which explicit authorization has been granted.
