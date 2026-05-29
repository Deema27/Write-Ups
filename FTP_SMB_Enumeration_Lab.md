# Lab 1 Report

## Table of Contents

* [Reconnaissance](#reconnaissance)
* [Target Interaction](#target-interaction)
* [Flags Retrieved](#flags-retrieved)

---

# Reconnaissance

## Machine 014400A1

An Nmap scan was performed against the target machine to identify open ports and exposed services. The scan revealed that only the **FTP service** was accessible, significantly limiting the attack surface and indicating that FTP would likely be the primary attack vector.

The exposed FTP service suggested the possibility of weak authentication, anonymous login access, or improperly configured file permissions.

<img width="1078" height="750" alt="image" src="https://github.com/user-attachments/assets/0606c0ac-3130-40a7-a9c4-c3e79bd744d1" />

---

## Machine 014700A4

A similar Nmap scan was executed against the second target machine. Unlike the first system, this host exposed multiple services, including:

* **RDP (Remote Desktop Protocol)**
* **SMB (Server Message Block)**

The presence of SMB suggested the possibility of accessible file shares, weak credentials, or misconfigured permissions that could potentially allow unauthorized access.

<img width="834" height="975" alt="image" src="https://github.com/user-attachments/assets/5c817bf0-aa1d-40ed-9737-9c4e13edf075" />

---

# Target Interaction

## Commands and Tools Used

### Machine 014400A1

* `ftp` was used to connect to the FTP service.
* Logged in using the **anonymous** account without requiring a password.

### Machine 014700A4

* `smbmap` was used to enumerate SMB shares and permissions.
* `crackmapexec` was used to test credentials and enumerate authentication weaknesses.

---

# Vulnerabilities Identified

| Machine  | Vulnerability                                     |
| -------- | ------------------------------------------------- |
| 014400A1 | Anonymous FTP login enabled                       |
| 014700A4 | Administrator account accessible with no password |

---

# Interactions with the Targets

## Machine 014400A1

### Step 1 — Accessing the FTP Server

Connected to the FTP service using the `ftp` command and authenticated successfully using the anonymous account.

This demonstrated that the server allowed unauthenticated access to remote users.

<img width="572" height="353" alt="image" src="https://github.com/user-attachments/assets/cd6b8238-0ed1-4180-9d12-bfcacb556c5c" />

---

### Step 2 — Enumerating Files

Executed the `ls` command to list the contents of the FTP directory. During enumeration, the file `flag.txt` was discovered.

<img width="788" height="150" alt="image" src="https://github.com/user-attachments/assets/be2061a6-c366-43fe-896c-f154b2edf61a" />

---

### Step 3 — Setting the Download Location

Changed the local FTP download directory to the Pwnbox temporary directory in preparation for downloading the target file.

<img width="400" height="86" alt="image" src="https://github.com/user-attachments/assets/83a76411-2bd3-4a79-af64-06219f73ab98" />

---

### Step 4 — Downloading the Flag

Used the `get` command to download `flag.txt` from the remote FTP server to the local machine.

<img width="788" height="73" alt="image" src="https://github.com/user-attachments/assets/829ea8b8-883c-4fc0-b7e1-fb014b873f17" />

---

### Step 5 — Closing the FTP Session

Exited the FTP session after successfully downloading the file.

<img width="197" height="84" alt="image" src="https://github.com/user-attachments/assets/cebc885a-3f6d-4088-a9a6-e46a94a9a5a2" />

---

### Step 6 — Navigating to the Downloaded File

Changed directories to the temporary folder containing the downloaded flag file.

<img width="286" height="53" alt="image" src="https://github.com/user-attachments/assets/2c32440b-eeb8-4cf7-949a-d257bacff9f6" />

---

### Step 7 — Reading the Flag

Used the `cat` command to display the contents of `flag.txt` and capture the flag value.

<img width="502" height="83" alt="image" src="https://github.com/user-attachments/assets/e55c385a-6130-46a6-b2fd-9b7d9662fcce" />

---

## Machine 014700A4

### Step 1 — Credential Discovery

Used `crackmapexec` to test credentials against the SMB service. The tool revealed that the user account **aartjan** used the weak password:

```bash
123456
```

<img width="916" height="46" alt="image" src="https://github.com/user-attachments/assets/5eda9ce7-2a4e-466f-ba71-ac5bac1cd7f5" />

---

### Step 2 — Testing SMB Access with User Credentials

Attempted to enumerate SMB shares using the discovered `aartjan` credentials. Although authentication succeeded, the account lacked sufficient permissions to access the `C:\` share.

<img width="863" height="166" alt="image" src="https://github.com/user-attachments/assets/7fad4b3b-15bd-4305-974f-ec33ad620c5f" />

---

### Step 3 — Accessing SMB with Administrator Account

Attempted authentication using the built-in **Administrator** account with a blank password. Authentication succeeded, granting full access to the `C:\` share.

This indicated a critical security misconfiguration involving an unprotected administrative account.

<img width="863" height="161" alt="image" src="https://github.com/user-attachments/assets/747823ef-a6e5-4bcb-97aa-c25a3bd4eaf5" />

---


### Step 4 — Verifying Full File System Access

Enumerated the contents of the root `C:\` directory to confirm unrestricted administrative access to the target system.

<img width="825" height="413" alt="image" src="https://github.com/user-attachments/assets/e3af9af2-f7c0-4099-b020-b8c579c69354" />

---

### Step 5 — Navigating to the Administrator Desktop

Listed the contents of the Administrator user's Desktop directory and located the target file `flag.txt`.

<img width="788" height="230" alt="image" src="https://github.com/user-attachments/assets/970ddae3-7472-46f5-beca-6d7784be09ea" />

---

### Step 6 — Reading the Flag File

Used the Windows `type` command to display the contents of `flag.txt` remotely through the SMB share.

<img width="863" height="42" alt="image" src="https://github.com/user-attachments/assets/047cbb3f-1f08-4362-afe3-9ab421dc39f3" />

---

# Flags Retrieved

## Machine 014400A1

The flag was successfully retrieved by downloading `flag.txt` through the anonymous FTP connection and reading the file locally.

```text
035db21c881520061c53e0536e44f815
```

<img width="502" height="83" alt="image" src="https://github.com/user-attachments/assets/3afeebbe-73ef-42aa-8f1f-6d6e54fc64e9" />

---

## Machine 014700A4

The flag was retrieved by leveraging unrestricted SMB access through the Administrator account and remotely reading `flag.txt`.

```text
951fa96d7830c451b536be5a6be008a0
```

<img width="788" height="38" alt="image" src="https://github.com/user-attachments/assets/03af8099-b811-4460-bb10-c5a2d83da6a6" />

---

