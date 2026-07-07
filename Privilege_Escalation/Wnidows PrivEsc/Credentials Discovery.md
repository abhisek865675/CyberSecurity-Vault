# Windows Privilege Escalation: Credential Discovery

---

##  Attack Methodology Overview

When operating on an intermediate or complex target, direct kernel exploits or software bugs might be absent. Attacker logic shifts to **Post-Exploitation Enumeration** centered on human error: passwords left in plaintext config files, memory, registries, or session history.



```
              [ ENUMERATION PHASE ]
                       │
   ┌───────────────────┼───────────────────┐
   ▼                   ▼                   ▼
```

┌──────────────┐ ┌──────────────┐ ┌─────────────────┐ │ PowerShell │ │ Unattended │ │ Windows │ │ History File │ │ Config Files │ │ Registry Hive │ └──────┬───────┘ └──────┬───────┘ └────────┬────────┘ │ │ │ └──────────────────┼────────────────────┘ ▼ [ Plaintext Credentials Found ] │ ▼ [ Execute: `runas` / `xfreerdp` ] │ ▼ [ ADMINISTRATOR ACCESS CAPTURED ]


---

## 1. PowerShell Console History File

By default, modern Windows environments running PowerShell 5.0+ keep a log of all commands executed inside a user's terminal session. If an administrator typed a password directly into a script parameter, it resides here.

### Target Log Path
```
%userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
````

### Enumeration & Read Command (CMD / PowerShell)

**PowerShell**
```
type "$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt"
```

- **Attacker Logic:** Look out for patterns such as `net user`, `Invoke-Expression`, or inline deployment credentials containing `-Password` arguments.
    

## 2. Unattended Installation Configuration Files

When system administrators deploy Windows machines at scale automatically, they utilize answer files (`Unattend.xml`). These automated configuration maps frequently contain the explicit local default administrator credentials inside the execution code block.

### Common File Vectors

System administrators often forget to purge these files after configuration:

- `C:\Windows\Panther\Unattend.xml`
    
- `C:\Windows\Panther\Unattended.xml`
    
- `C:\Windows\Sysprep\sysprep.xml`
    

### Target Search & Extraction (CMD)

```
findstr /si password c:\*.xml c:\*.ini c:\*.txt
```

- `/s`: Search across current directory and all subdirectories.
    
- `/i`: Ignore case sensitivity matching patterns.
    

## 3. Windows Registry Credential Auditing

Windows configurations often write administrative credentials inside the Local Machine (`HKLM`) hives to sustain operations or run deployment auto-logins.

### Vector A: Windows AutoLogon Registry Check

When a Windows enterprise workstation is configured to bypass the login box automatically after restarts, credentials are explicitly stored plaintext here:

```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
```

### Vector B: PuTTY and Third-Party SSH Sessions

If target administrative users utilized tool suites like PuTTY, saved sessions might map connection scripts or structural metadata:

```
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions" /s
```

## 4. Windows Credential Manager Interception

Windows includes an enterprise-grade safe utility ("Credential Manager") to securely vault structural authentications, domain credentials, and network token passphrases.

### Enumeration Command


```
cmdkey /list
```

- **Exploitation Blueprint:** If you identify target active profiles saved inside the vault, you can explicitly leverage the system environment properties to trigger execution using those exact profiles via the run-as system components.
    

### Forced Execution utilizing Cached Profile Tokens

```
runas /savecred /user:ACCESS_USER "cmd.exe"
```

- `/savecred`: Directs Windows to enforce authorization utilizing credentials previously cached by the administrative user.


---
