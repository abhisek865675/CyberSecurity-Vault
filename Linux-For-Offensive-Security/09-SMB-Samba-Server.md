# SMB (Samba) Server Setup & Exploitation 📂

SMB (Server Message Block) is the protocol used by Windows for file and printer sharing. **Samba** is the Linux implementation of this protocol. In a VAPT context, SMB is the "king" of internal network enumeration because it is almost always misconfigured.

---

## 1. Installation & Setup
On Debian/Kali-based systems:

1. **Install:** `sudo apt update && sudo apt install samba`
2. **Create a Share Directory:**
```bash
   sudo mkdir -p /srv/samba/shared
   sudo chmod 777 /srv/samba/shared
```

2. **Configure:** Edit `/etc/samba/smb.conf` and add this to the bottom:
    
    Ini, TOML
    
    ```
    [AnonymousShare]
       path = /srv/samba/shared
       browseable = yes
       read only = no
       guest ok = yes
    ```
    
3. **Restart:**
    
    Bash
    
    ```
    sudo systemctl restart smbd
    ```
    

## 2. VAPT Strategy: SMB Enumeration & Exploitation

When you find an SMB port (139/445) open during a pentest, never skip it. Use these tools to extract information.

### A. Enumeration

- **`smbclient`:** The command-line client to interact with shares.
    
    - _Connect:_ `smbclient //<TARGET_IP>/AnonymousShare`
        
- **`enum4linux`:** The automated "Swiss Army Knife" for SMB. It collects:
    
    - Users, Groups, Shares, Password Policy, and OS info.
        
    - _Run:_ `enum4linux -a <TARGET_IP>`
        

### B. The "Null Session" Vulnerability

A Null Session occurs when a server allows an anonymous user to connect to the IPC$ (Inter-Process Communication) share without a username or password.

- **Why it's critical:** If an SMB server allows a Null Session, you can often pull the entire list of usernames, group memberships, and share permissions from the server with zero authentication.
    
- **Exploit:** `smbclient //<TARGET_IP>/IPC$ -U "" -N` (Connect as empty user, no password).
    

## 3. Advanced Attacks

1. **Brute Force:** If you enumerate a list of usernames, use `hydra` to attack the SMB service: `hydra -l username -P /path/to/wordlist.txt smb://<TARGET_IP>`
    
2. **SMB Relay Attack:** If the target does not have "SMB Signing" enabled, you can perform an **NTLM Relay Attack**. You intercept authentication attempts from other users on the network and "relay" them to the target server to gain their access rights.
    
3. **Hidden Shares:** Check for hidden administrative shares like `C$` or `ADMIN$` (usually found on Windows systems). These are often accessible if you have compromised a domain account.
    

## 4. Key Security Takeaways

- **Disable SMBv1:** Always. It is ancient and highly vulnerable to exploits like _EternalBlue_.
    
- **Enable SMB Signing:** This forces the server to verify every packet, effectively killing Relay Attacks.
    
- **Restrict Access:** Never make shares `guest ok = yes` unless it is a public-only kiosk.


---

