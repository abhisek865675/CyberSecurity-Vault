
## 1. SMB Share (The Native Way)

_Best for: Moving folders or large datasets between machines on the same network._

### Setup (Source Machine)

#### In Gui Mode 

```
1. Right-click the folder > Properties > Sharing tab.

2. Select Advanced Sharing > Check Share this folder.

3. Click Permissions > Add Everyone (Grant Read/Write access).

4. Identify the share path: \\<SOURCE_IP>\ShareName
```

### In Cli (CMD or Powershell)

`Folder creation`

```
mkdir C:\SharedFolder
```

` Folder ko "MyShare" naam se share karo`

```
net share MyShare=C:\SharedFolder /grant:Everyone,FULL
```
### Execution (Target Machine)

Open CMD or PowerShell:

```
# Map the shared folder as a network drive (Z:)
net use Z: \\<SOURCE_IP>\ShareName /user:Username Password

# Copy the file
copy Z:\filename.txt C:\Users\Public\
```

### i. Configure the Receiver (Target Machine)

By default, WinRM is disabled on Windows. You must enable it to accept remote connections.

**Steps (Run in PowerShell as Administrator):**

`Enable WinRM:`
### PowerShell

```
Enable-PSRemoting -Force
```


#### ii. **Allow Remote Access (If on a non-domain network):**
  
PowerShell
  
```
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "<SENDER_IP>" -Force Restart-Service WinRM
```

`2. **Check Firewall:** Ensure the WinRM port (5985/5986) is open.`
    
    PowerShell
    
  ```
   New-NetFirewallRule -Name "WinRM-HTTP" -DisplayName "WinRM HTTP" -Enabled True -Protocol TCP -Action Allow -LocalPort 5985   
  ```

## 2. Configure the Sender (Controller Machine)

The Sender initiates the session and handles the authentication to the Target.

**Steps (Run in PowerShell):**

1. **Define Target and Credentials:**
    
    PowerShell
    
    ```
    $targetIP = "<TARGET_IP>"
    $cred = Get-Credential  # Enter username/password of the target machine
    ```
    
2. **Establish the Session:**
    
    PowerShell
    
    ```
    $session = New-PSSession -ComputerName $targetIP -Credential $cred
    ```
    
3. **Transfer the File:**
    
    PowerShell
    
    ```
    # Copy file FROM Sender TO Target
    Copy-Item -Path "C:\Source\localfile.txt" -Destination "C:\Destination\remotefile.txt" -ToSession $session
    ```
    
4. **Close the Session:**
    
    PowerShell
    
    ```
    Remove-PSSession $session
    ```
## 3. SCP (OpenSSH Built-in)

_Best for: Encrypted, secure transfers. Available by default on Windows 10/11._

### Execution (Sender)

```
# Push file to remote machine
scp C:\local\path\file.txt Username@<TARGET_IP>:C:\Users\Public\
```

_Requirement: The OpenSSH Server service must be running on the target machine._

## 4. BITSAdmin (Background Intelligent Transfer)

_Best for: Large files, unstable network conditions, and bypassing restrictive firewalls._

### Execution (Receiver)

_Assuming the sender has an HTTP/IIS server running:_

DOS

```
# Download file in the background
bitsadmin /transfer "MyDownloadJob" /download /priority normal http://<SENDER_IP>/file.exe
```