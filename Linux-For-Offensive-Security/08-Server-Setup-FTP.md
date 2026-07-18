# FTP Server Setup & VAPT Perspective (vsftpd) 📁

FTP (File Transfer Protocol) is used for transferring files between a client and a server. By default, it is **unencrypted**, making it a primary target for attackers.

---

## 1. Installation & Basic Setup
On Debian/Kali-based systems, we use **vsftpd** (Very Secure FTP Daemon):

1. **Install:** 
```bash
   sudo apt update && sudo apt install vsftpd
   
```

2. **Enable and Start:**
    
    Bash
    
    ```
    sudo systemctl enable vsftpd
    sudo systemctl start vsftpd
    ```
    
3. **Check Status:**
    
    Bash
    
    ```
    sudo systemctl status vsftpd
    ```
    

## 2. Configuration (`/etc/vsftpd.conf`)

To make an FTP server functional (or "hackable" for lab practice), edit the configuration file:

- **Anonymous Login (Highly Insecure):**
    
    - `anonymous_enable=YES`
        
    - _VAPT Note:_ If this is `YES`, anyone can log in as user "anonymous" with no password. Always check for this first.
        
- **Write Access:**
    
    - `write_enable=YES`
        
    - _VAPT Note:_ If anonymous login AND write access are both `YES`, an attacker can upload a web shell to the server and gain remote command execution.
        
- **Local User Login:**
    
    - `local_enable=YES`
        
    - Allows users with system accounts to log in via FTP.
        

## 3. The VAPT Strategy (Hacking FTP)

When you encounter an FTP server during a pentest, follow this workflow:

### Step 1: Enumeration

- **Connect:** `ftp <TARGET_IP>`
    
- **Try Anonymous:** Use username `anonymous` and password `anonymous` (or blank).
    
- **Banner Grabbing:** Use `nc <TARGET_IP> 21` to see the service version. Search online (Exploit-DB) for vulnerabilities related to that specific version.
    

### Step 2: The Attack Vectors

1. **Cleartext Credential Sniffing:** Since FTP does not use encryption (unless using FTPS), you can use `Wireshark` or `tcpdump` to capture the login credentials in transit if you are on the same network.
    
2. **Brute Force:** If anonymous is disabled, use `hydra` to brute-force: `hydra -l user -P /path/to/wordlist.txt ftp://<TARGET_IP>`
    
3. **File Upload (Execution):** If you can upload files, try to upload a `.php` or `.asp` file. If the web server interprets it, you get a **Reverse Shell**.
    

### Step 3: File Hunting

- Always search the FTP share for:
    
    - Backup files (`.bak`, `.zip`)
        
    - Configuration files (`config.php`, `web.config`)
        
    - SSH keys (`id_rsa`)
        
    - Database dumps
        

## 4. Key Management Commands

- **Restarting Service:** `sudo systemctl restart vsftpd`
    
- **Logs:** Check `/var/log/vsftpd.log` for activity. If you compromise a machine, check this log to see if others have been there before you.



---