#  Postfix (SMTP) Server Setup & Enumeration 📧

SMTP (Simple Mail Transfer Protocol) is used for sending emails. Postfix is the most popular open-source MTA (Mail Transfer Agent) for Linux. From a pentester's perspective, SMTP is invaluable for User Discovery.

---

## 1. Installation & Basic Setup
On Debian/Kali-based systems:

1. **Install and Start:**
```bash
   sudo apt update && sudo apt install postfix -y
   sudo systemctl restart postfix
```

## 2. VAPT Strategy: SMTP Enumeration

SMTP servers often have "built-in" commands that can tell you exactly which usernames exist on a system. This is a common method for gathering a target list before launching a password brute-force attack.

### A. Manual Enumeration (The `VRFY` Command)

The `VRFY` (Verify) command asks the SMTP server if a specific user account exists.

1. **Connect:** `nc <TARGET_IP> 25`
    
2. **Interact:**
    
    Plaintext
    
    ```
    VRFY root
    VRFY admin
    VRFY testuser
    ```
    

- **Success:** If it returns `250 2.1.5`, the user exists.
    
- **Failure:** If it returns `550 5.1.1`, the user does not exist.
    

### B. Automated Enumeration

Manually typing `VRFY` is slow. Use automated tools to scan for users:

- **`smtp-user-enum`:** A classic tool for this specific purpose.
    
    Bash
    
    ```
    smtp-user-enum -M VRFY -U /path/to/usernames.txt -t <TARGET_IP>
    ```
    
    - `-M`: Specifies the method (VRFY, RCPT, EXPN).
        
    - `-U`: The wordlist of potential usernames.
        
    - `-t`: The target IP.
        

## 3. Advanced SMTP Attacks

1. **Open Relay Exploitation:**
    
    - An "Open Relay" is an SMTP server misconfigured to send mail for anyone.
        
    - **Test:** `nmap -p25 --script smtp-open-relay <TARGET_IP>`
        
2. **Brute Force:**
    
    - Once you have a valid list of usernames, use `hydra` to brute-force the SMTP login:
        
    
    Bash
    
    ```
    hydra -l valid_user -P passwords.txt <TARGET_IP> smtp
    ```
    

## 4. Key Security Takeaways

- **Disable VRFY/EXPN:** Always disable these to prevent user enumeration.
    
- **Require Authentication:** Never allow sending mail without authentication (SASL).
    
- **Use TLS:** Encrypt SMTP traffic with TLS.
 
 