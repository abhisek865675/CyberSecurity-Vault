
# Windows Privilege Escalation: Abusing User Privileges (Tokens)

- **Core Concept:** Exploiting built-in Windows User Privileges / Access Tokens assigned to specific service accounts (like IIS or local service accounts) to bypass standard restriction rings and escalate to `NT AUTHORITY\SYSTEM`.

---

##  The Core Concept of Access Tokens

When a process or a user initializes in Windows, the kernel assigns it an **Access Token** (like a digital security badge). This token defines *who* the user is and *what special rights* they have inside the OS kernel infrastructure. 

Instead of searching for file system bugs, this vector abuses **intended Windows features** that have high-impact security implications if misconfigured or assigned to a compromise-prone service account (e.g., web servers running under `local service`).

```

```
                [ INITIAL ACCESS GAINED ]
                           │
                           ▼
                [ Run: `whoami /priv` ]
                           │
     ┌─────────────────────┼─────────────────────┐
     ▼                     ▼                     ▼
```

┌─────────────────┐ ┌─────────────────┐ ┌──────────────────┐

│ SeImpersonate │ │ SeBackup/ │ │ SeDebugPrivilege │

│ Privilege │ │ SeRestore │ │ (Process Memory │

│ (PrintSpoofer/ │ │ (Read/Write All │ │ Injection) │

│ GodPotato) │ │ System Files) │ │ │

└────────┬────────┘ └────────┬────────┘ └────────┬─────────┘

│ │ │

└─────────────────────┼─────────────────────┘

▼

[ NT AUTHORITY\SYSTEM CAPTURED ]

````

---

## 1. Initial Enumeration: Finding the Golden Tokens

The very first diagnostic command executed after landing a shell on a Windows machine to audit token security parameters:

```
whoami /priv
````

### Critical Targets Table

Look specifically for these privileges and their active state:

| **Privilege Name**       | **Default Behavior**                          | **Exploitation Vector / Impact**                                                      |
| ------------------------ | --------------------------------------------- | ------------------------------------------------------------------------------------- |
| `SeImpersonatePrivilege` | Impersonate a client after authentication     | Force a SYSTEM process to connect to a local pipe and steal its token.                |
| `SeBackupPrivilege`      | Back up files and directories (Read Override) | Bypasses Access Control Lists (ACLs) to read _any_ file (including SAM/SYSTEM hives). |
| `SeDebugPrivilege`       | Debug programs / processes                    | Allows process injection into system components like `lsass.exe`.                     |

## 2. Deep Dive: Exploiting SeImpersonatePrivilege

If `SeImpersonatePrivilege` is marked as **Enabled** (often found when exploiting service accounts like `iisapppool` or network service), you can capture and impersonate elevated tokens.

### The Mechanism

1. The attacker creates a rogue Named Pipe (an internal OS communication channel).
    
2. The attacker uses an exploit binary to trick a system-level process (like the Spooler service) into interacting or authenticating against this rogue pipe.
    
3. Once the system process hits the pipe, the attacker drops down its execution barrier, intercepts the high-privilege token, and clones it to spawn a shell.
    

### Step-by-Step Execution via PrintSpoofer / GodPotato

#### Step 1: Check System Compatibility

- **PrintSpoofer:** Extremely reliable on older architectures or specific builds (Windows 10 / Server 2016/2019).
    
- **GodPotato / MultiPotato:** Designed for newer OS build variations where older pipe behaviors are patched.
    

#### Step 2: Run the Exploit Target Tool

Transfer your binary (`PrintSpoofer.exe` or `GodPotato.exe`) to a writable target directory like `C:\Windows\Tasks\` or `C:\Users\Public\`.

Execute PrintSpoofer to spawn an interactive terminal:

DOS

```
PrintSpoofer.exe -i -c cmd.exe
```

- `-i`: Interactive execution flag.
    
- `-c cmd.exe`: Spawns a new command prompt inside the elevated security ring.
    

#### Step 3: Verify High-Privilege Context

DOS

```
C:\Windows\system32> whoami
nt authority\system
```

## 3. Deep Dive: Exploiting SeBackupPrivilege / SeRestorePrivilege

If the user account possesses `SeBackupPrivilege`, the operating system allows it to read any file on the drive, completely ignoring standard NTFS read restrictions.

### Step-by-Step Extraction of Account Database (SAM)

Since we can read any system-protected sector, we can extract the local credentials database manually:

#### Step 1: Save the SAM Hive

DOS

```
reg save HKLM\SAM C:\Windows\Tasks\sam.hive
```

#### Step 2: Save the SYSTEM Hive (Required to decrypt the SAM database)


```
reg save HKLM\SYSTEM C:\Windows\Tasks\system.hive
```

#### Step 3: Extract Secrets on Kali Linux

Download both `.hive` files via your web shell or an SMB share to your Kali machine and parse the NTLM hashes:

Bash

```
secretsdump.py -sam sam.hive -system system.hive LOCAL
```

Once you obtain the `Administrator` account NTLM hash, pass the hash natively using `evil-winrm` or `psexec` to capture a system shell.


---
