# CTF

## Table of Contents
- [Challenge 1: Events Never Lie](#challenge-1-events-never-lie)
- [Challenge 2: False Utility](#challenge-2-false-utility)
- [Challenge 3: Network](#challenge-3-network)
- [Challenge 4: Status Board](#challenge-4-status-board)
- [Challenge 5: The Wire](#challenge-5-the-wire)

---



# Challenge 1: Events Never Lie

## Objective
Analyze Windows event logs using Chainsaw to identify malicious activity, reconstruct the attack timeline, and recover the challenge flag.

## Methodology

### Step 1 – Prepare the Logs
Downloaded and extracted the provided ZIP archive containing Windows EVTX logs.

### Step 2 – Identify Defender Tampering
Searched for Event ID 5001, which indicates Windows Defender Real-Time Protection was disabled.

### Step 3 – Investigate Malicious PowerShell Activity
Reviewed Chainsaw Sigma detections and identified malicious PowerShell execution.

### Step 4 – Determine Log Source
Located the malicious payload within the Microsoft-Windows-PowerShell log.

### Step 5 – Recover Obfuscation Key
Reviewed Event ID 4104 Script Block Logging events and identified XOR key 35.

## Findings

| Artifact | Value |
|-----------|---------|
| Defender Disabled | 2024-04-17 14:27:08 |
| Malicious PowerShell Detected | 2024-04-17 14:28:00 |
| Log Source | Microsoft-Windows-PowerShell |
| XOR Key | 35 |

## Flag Retrieved
HTB{P0w3rSh3ll_s0m3t1m3s_l13_th0ugh!_641988b99aaeee4d4ad0dac993379d3d}

## Analysis
The attacker first disabled Windows Defender Real-Time Protection and then executed an obfuscated PowerShell payload. Event log analysis revealed the complete attack timeline and the XOR key used to conceal the malicious script.

---

# Challenge 2: False Utility

## Objective
Analyze a suspicious binary and recover the embedded flag.

## Methodology

### Step 1 – Static Analysis
Extracted the challenge files and examined the binary named `cat` using the strings utility.

### Step 2 – Identify Binary Characteristics
Observed indicators showing the binary was compiled for ARM64 architecture and processed external data.

### Step 3 – Reverse the Encoding
Developed a Python script to reverse the custom encoding routine.

### Step 4 – Decode the Data
Applied the rolling XOR routine and final XOR with 0x55 to recover plaintext.

## Findings

| Artifact | Observation |
|-----------|-------------|
| Binary Type | ARM64 Executable |
| Encoding Method | Rolling XOR + XOR 0x55 |
| Analysis Type | Static Analysis |

## Flag Retrieved
HTB{WHY_TH3Y_L34V1N_M4RK5}

## Analysis
The challenge demonstrated reverse engineering through static analysis. Understanding the encoding routine allowed recovery of the hidden flag without executing the binary.

---

# Challenge 3: Network

## Objective
Analyze Modbus traffic within a packet capture and recover the hidden message.

## Methodology

### Step 1 – Extract the Packet Capture
Downloaded and extracted the provided PCAP file.

### Step 2 – Identify Modbus Traffic
Used tshark to filter packets containing Modbus communications.

### Step 3 – Analyze Register Writes
Observed a sequence of hexadecimal values transmitted through Modbus write operations.

### Step 4 – Decode ASCII Values
Converted the extracted hexadecimal values into ASCII characters using Python.

### Step 5 – Reconstruct the Message
Recovered the complete hidden message transmitted across the network.

## Findings

| Artifact | Observation |
|-----------|-------------|
| Protocol | Modbus |
| Function Code | 16 |
| Data Type | ASCII Characters |
| Transmission Method | Register Values |

## Flag Retrieved
HTB{MOd8u5_23rO_3CnRYp7IoNT@x30}

## Analysis
This challenge demonstrated how industrial control system protocols can be abused to transmit information covertly through legitimate network traffic.

---

# Challenge 4: Status Board

## Objective
Exploit a NoSQL injection vulnerability and extract sensitive information from the backend database.

## Methodology

### Step 1 – Analyze Authentication
Examined the login functionality and suspected a MongoDB backend.

### Step 2 – Perform Authentication Bypass
Submitted a NoSQL injection payload using the `$ne` operator.

### Step 3 – Confirm Administrative Access
Verified successful authentication as the administrator.

### Step 4 – Blind Enumeration
Developed a Python script that used regular expressions to recover the flag one character at a time.

### Step 5 – Recover the Flag
Repeated successful login attempts until the entire flag was reconstructed.

## Findings

| Artifact | Observation |
|-----------|-------------|
| Vulnerability | NoSQL Injection |
| Authentication Bypass | Successful |
| Extraction Method | Blind Regex Enumeration |

## Flag Retrieved
HTB{t0b3_5qL_0r_n05qL_7h4t_is_th3_Q}

## Analysis
Improper input validation allowed MongoDB query manipulation, resulting in authentication bypass and extraction of sensitive information.

---

# Challenge 5: The Wire

## Objective
Perform packet analysis using Wireshark and answer a series of forensic questions.

## Methodology

### Step 1 – Open Packet Capture
Loaded the PCAPNG file into Wireshark.

### Step 2 – Analyze Statistics
Reviewed packet counts, byte counts, and traffic conversations.

### Step 3 – Apply Display Filters
Used Wireshark filters to isolate traffic between specific hosts.

### Step 4 – Examine Protocol Data
Investigated Ethernet, IPv4, TCP, and HTTP metadata.

### Step 5 – Answer Challenge Questions
Collected all required answers and submitted them to the challenge portal.

## Key Findings

- Packets from 192.168.1.103 to 151.101.129.140: 679
- Bytes from 192.168.1.108 to 116.203.58.90: 772
- Packets with length 121 bytes: 13
- Packets containing 151.101.129.140: 1485
- TCP packets: 13679
- Broadcast MAC Address: ff:ff:ff:ff:ff:ff
- Host Header: detectportal.firefox.com
- Device MAC: 08:00:27:ed:96:4b

## Flag Retrieved
HTB{I_g0t_th3_w1r3_4nd_y0u_g0t_th3_fl@g!!_b2e7ceb8ee35e95fca0dd19c1a81a59e}

## Analysis
The challenge reinforced packet analysis, traffic filtering, protocol inspection, and network forensic investigation techniques.

---

