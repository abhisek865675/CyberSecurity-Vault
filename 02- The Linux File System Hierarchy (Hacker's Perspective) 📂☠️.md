#  The Linux File System Hierarchy 

Understanding the Linux file system is the first step in local enumeration. Unlike Windows, which uses drive letters (`C:\`, `D:\`), Linux uses a single hierarchical tree structure starting from the **Root directory (`/`)**. 

As a penetration tester, you do not need to memorize every single folder, but you must know exactly where to look for passwords, logs, configuration files, and places to hide your payloads.

---

## 1. The Core Directories

### `/` (The Root)
The absolute top level of the file system. Everything starts here. 
* *Note:* Do not confuse the `/` (Root directory) with `/root` (The home directory of the root user).

### `/bin` and `/sbin` (Binaries)
These directories contain the essential command-line utilities and executable programs. 
* `/bin` (User Binaries): Contains standard commands like `ls`, `cat`, `echo`, `nc`, `python`.
* `/sbin` (System Binaries): Contains administrative commands like `fdisk`, `ifconfig`, `ip`.
* **🔥 Red Team Value:** This is where you look for "Living off the Land" (LotL) binaries. If you need a reverse shell, check if `nc` (Netcat), `python`, `perl`, or `bash` are available here.

---

## 2. The Goldmines (Where to find Loot)

### `/etc` (Configurations)
This directory holds system-wide configuration files. It is the most critical directory for enumeration.
* **🔥 Red Team Value:**
  * `/etc/passwd`: Contains a list of all users on the system (Readable by everyone).
  * `/etc/shadow`: Contains the actual hashed passwords of users (Should only be readable by root. If you can read this, you can crack the hashes offline).
  * `/etc/sudoers`: Defines which users can run commands as root using `sudo`.

### `/home` and `/root` (User Directories)
These contain the personal files, configurations, and hidden files of users.
* `/home/username`: For standard users.
* `/root`: For the root administrator.
* **🔥 Red Team Value:** Look for hidden files (files starting with a dot `.`).
  * `.bash_history`: Users often accidentally type passwords or sensitive commands here.
  * `.ssh/id_rsa`: Private SSH keys. If you steal this, you can log in as that user without a password.

### `/var` (Variable Data & Logs)
Contains files to which the system writes data during its operation (logs, web files, databases).
* **🔥 Red Team Value:**
  * `/var/log`: Contains system logs (e.g., `/var/log/auth.log` shows failed login attempts). You may need to clear these to cover your tracks.
  * `/var/www/html`: The default directory for Apache/Nginx web servers. If you have write access here, you can upload a PHP web shell to execute remote commands.

### `/opt` (Optional Software)
Used for installing third-party software that doesn't come with the standard Linux distribution.
* **🔥 Red Team Value:** Third-party applications often have weak permissions or outdated, vulnerable versions. Always check `/opt` for custom scripts that might be running as root via a cronjob.

---

## 3. The Playgrounds (Where to Execute Payloads)

When you gain a low-level shell on a Linux box, you usually cannot create files in random directories because of restricted permissions. However, the system requires certain directories to be **world-writable** (meaning any user can create and execute files there).

### `/tmp` (Temporary Files)
Used by the system and applications to store temporary data.
* **🔥 Red Team Value:** This directory is usually world-writable (`rwxrwxrwt`). If you need to download a Linux enumeration script (like `linpeas.sh`) or a privilege escalation exploit to the target machine, always save it in `/tmp`. Files here are deleted upon system reboot.

### `/dev/shm` (Shared Memory)
This is not actually a directory on the hard drive; it is a RAM-backed temporary file storage.
* **🔥 Red Team Value:** Like `/tmp`, it is world-writable. Because it exists in RAM, writing payloads here is extremely fast, and the data leaves no trace on the physical hard drive once the system shuts down (excellent for stealth).

---

## 4. Virtual Directories (System Info)

### `/proc` (Processes & Kernel Data)
This is a virtual filesystem created dynamically by the kernel. It contains real-time information about running processes and hardware.
* **🔥 Red Team Value:**
  * `/proc/version`: Shows the exact kernel version (useful for finding kernel exploits like Dirty COW).
  * `/proc/net/tcp`: Can show active network connections even if tools like `netstat` are missing.

### `/dev` (Device Files)
In Linux, "Everything is a file," including hardware. This directory contains device nodes.
* `/dev/null`: The "black hole" of Linux. Sending output here deletes it instantly (e.g., `command > /dev/null 2>&1` hides all errors from your screen).