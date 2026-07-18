# Linux File System Architecture & Root Directory Deep Dive

To effectively navigate, attack, or secure a Linux system, you must understand how data is stored on the disk and how the operating system organizes its folders. Linux follows a strict standard called the **Filesystem Hierarchy Standard (FHS)**.

---

## 1. What File Systems Does Linux Use?

In Windows, the standard file system format is NTFS. Linux, however, supports a wide variety of file systems depending on the distribution and the specific needs of the server.

* **ext4 (Fourth Extended Filesystem):** The absolute standard and default file system for most Linux distributions, including Kali Linux, Ubuntu, and Debian. It is stable, fast, and supports large file sizes.
* **XFS:** The default file system for Red Hat Enterprise Linux (RHEL) and CentOS. It is highly optimized for extremely large servers and parallel I/O operations.
* **Btrfs (B-Tree Filesystem):** A modern file system used in systems like Fedora. It supports advanced features like built-in snapshots (similar to virtualization snapshots) and transparent compression.
* **Swap:** This is not a traditional file system. It is a dedicated partition (or file) on the hard drive used as "Virtual RAM." When the physical RAM is full, the Linux kernel moves inactive memory pages to the Swap space to prevent the system from crashing.

---

## 2. The Root Directory (`/`) Structure

In Linux, there are no `C:\` or `D:\` drives. Everything is mounted under a single, unified tree starting at the **Root Directory (`/`)**. 

Here is the complete breakdown of every standard directory found under `/`, its exact purpose, and its relevance to a penetration tester.

### ⚙️ System & Execution Directories
* **`/bin` (User Binaries):** Contains essential command-line executable files available to all users (e.g., `ls`, `cat`, `bash`, `ping`). 
  * *VAPT Note:* This is where you look for tools to spawn reverse shells (like `nc` or `python`).
* **`/sbin` (System Binaries):** Contains essential administrative executables that generally require `root` privileges to run (e.g., `fdisk`, `reboot`, `iptables`).
* **`/lib`, `/lib32`, `/lib64` (Shared Libraries):** Contains the shared library files needed by the binaries in `/bin` and `/sbin`. These are the Linux equivalent of Windows `.dll` files.
  * *VAPT Note:* Advanced attackers use techniques like `LD_PRELOAD` to hijack these libraries and escalate privileges.

### 🧠 Core OS & Hardware Directories
* **`/boot` (Bootloader Files):** Contains everything required to start the system, including the Linux Kernel (`vmlinuz`), the Initial RAM Disk (`initrd`), and the GRUB bootloader configuration.
* **`/dev` (Device Files):** In Linux, "Everything is a file." This directory contains special files that represent physical hardware devices attached to the system (e.g., `/dev/sda` for the main hard drive, `/dev/tty` for the terminal).
* **`/sys` (System File System):** Similar to `/dev`, this is a virtual file system that provides a detailed interface to the kernel and hardware configurations.

### 🔐 Configuration & User Directories
* **`/etc` (System Configurations):** The absolute most important directory for system administrators and hackers. It contains system-wide configuration files for all installed services.
  * *VAPT Note:* Look for `/etc/passwd` (list of users), `/etc/shadow` (hashed passwords), and `/etc/sudoers` (sudo permissions). Misconfigured files here directly lead to system compromise.
* **`/home` (User Home Directories):** Contains personal files, desktop settings, and hidden configurations for standard non-root users (e.g., `/home/rohit`).
  * *VAPT Note:* Always search for hidden files like `.bash_history` (which may contain typed passwords) and `.ssh/id_rsa` (private SSH keys).
* **`/root` (Superuser Home Directory):** The home directory exclusively for the `root` user. Do not confuse this with the `/` directory.

### 📂 Storage & Application Directories
* **`/var` (Variable Data):** Contains files whose sizes continuously grow while the system is running. 
  * *VAPT Note:* `/var/log` contains security and authentication logs (which you may need to clear). `/var/www/html` is the default web root for Apache/Nginx (prime target for uploading web shells).
* **`/usr` (User Programs):** The secondary hierarchy. Contains non-essential applications, documentation, and libraries installed for users (e.g., `/usr/bin/nmap`).
* **`/opt` (Optional Software):** Used for third-party, add-on software packages that do not come from the standard repository (e.g., `/opt/nessus` or custom enterprise software).
  * *VAPT Note:* Third-party tools often run as root but have weak permissions. This is a primary hunting ground for Privilege Escalation.
* **`/srv` (Service Data):** Contains site-specific data served by the system, such as FTP or SVN repository data.

### 🗑️ Temporary & Mount Directories
* **`/tmp` (Temporary Files):** A world-writable directory used by the system and applications to store temporary data. Files here are deleted upon reboot.
  * *VAPT Note:* Because it is world-writable (`rwxrwxrwt`), this is the default playground for hackers to download exploit scripts (like `linpeas.sh`) and execute local privilege escalation payloads.
* **`/mnt` (Mount Directory):** A temporary mount point used by system administrators to manually attach external hard drives or network file systems (NFS/SMB).
* **`/media` (Removable Media):** The directory where the OS automatically mounts removable media like USB drives and CD-ROMs.

### 👻 Virtual Directories (RAM-Based)
* **`/proc` (Process Information):** This directory does not exist on the hard drive; it is generated in RAM by the kernel. It contains real-time information about running processes (represented by numbered folders corresponding to PIDs) and system resources.
* **`/run` (Runtime Data):** A temporary filesystem (tmpfs) that stores system information detailing the state since the system was booted (e.g., process IDs of running daemons).