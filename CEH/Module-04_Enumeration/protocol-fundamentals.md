# Core Protocols & Enumeration Terminology 📖

Welcome to the **Protocol Fundamentals** guide. Before successfully enumerating (extracting data from) any service, it is crucial to understand what that service was originally designed to do and how it operates under normal conditions. 

When we understand a protocol's normal behavior, it becomes much easier to identify misconfigurations and vulnerabilities. Below is a breakdown of the most important protocols targeted during the enumeration phase:

---

## 1. SMB / NetBIOS (Server Message Block)
* **Ports:** 139 (NetBIOS), 445 (SMB over TCP)

**🧠 What is it and why was it built?** SMB is a network file-sharing protocol originally developed by Microsoft. Its primary purpose is to allow computers on a local network (LAN) to share files, printers, and serial ports seamlessly.

**🔍 Why and how do we enumerate it?** As ethical hackers, we target SMB because misconfigured shares can leak sensitive files, passwords, and complete lists of network users or groups. This is often achieved through a **"Null Session"** attack, where an attacker connects to the server without providing a username or password to extract this data.

**🛠️ Enumeration Tools:**
* **Enum4linux:** `enum4linux -a <target-ip>` (The ultimate tool that extracts almost everything).
* **Smbclient:** `smbclient -L <target-ip>` (Used to list available shares on the target).
* **Nmap:** `nmap --script smb-enum-users <target-ip>`

---

## 2. SNMP (Simple Network Management Protocol)
* **Ports:** 161, 162 (UDP)

**🧠 What is it and why was it built?** Imagine a network administrator managing 500 routers, switches, and servers. Checking them one by one is impossible. SNMP was created so admins can monitor device health, CPU usage, and network traffic from a central management dashboard.

**🔍 Why and how do we enumerate it?** SNMP uses a password called a **"Community String"** for authentication (often left as the default `public` or `private`). If we discover this string, we can extract the target device's entire routing table, ARP cache, and internal network architecture details.

**🛠️ Enumeration Tools:**
* **Snmpwalk:** `snmpwalk -v 1 -c public <target-ip>`
* **Snmpcheck:** `snmp-check <target-ip>`

---

## 3. SMTP (Simple Mail Transfer Protocol)
* **Port:** 25

**🧠 What is it and why was it built?** SMTP is the postman of the internet. It is the standard protocol used for sending emails from one mail server to another.

**🔍 Why and how do we enumerate it?** SMTP servers have built-in commands like `VRFY` (Verify user) and `EXPN` (Expand mailing list). We can abuse these commands to verify if specific email addresses or usernames actually exist within the target organization. This helps in building a valid user list for brute-force attacks or targeted social engineering (phishing).

**🛠️ Enumeration Tools:**
* **Manual Netcat:** `nc <target-ip> 25` (Once connected, manually type `VRFY root` or `VRFY admin`).
* **Nmap:** `nmap --script smtp-enum-users <target-ip>`
* **Metasploit:** `auxiliary/scanner/smtp/smtp_enum`

---

## 4. LDAP (Lightweight Directory Access Protocol)
* **Ports:** 389 (Cleartext), 636 (Secure)

**🧠 What is it and why was it built?** Large organizations use Microsoft Active Directory (AD) to manage thousands of employee accounts and computers. LDAP is the directory service protocol built to query and search this AD database. Think of it as a massive digital phonebook for the company network.

**🔍 Why and how do we enumerate it?** If LDAP is misconfigured and allows an **"Anonymous Bind,"** an attacker can query the server without any credentials. This can expose complete employee details, job roles, phone numbers, and the internal system architecture.

**🛠️ Enumeration Tools:**
* **Ldapsearch:** `ldapsearch -x -h <target-ip> -s base` (To attempt an anonymous bind).
* **Nmap:** `nmap -p 389 --script ldap-search <target-ip>`

---

## 5. NTP (Network Time Protocol)
* **Port:** 123 (UDP)

**🧠 What is it and why was it built?** Every computer, server, and firewall on a network needs its clock synchronized to the exact same time (which is crucial for accurately maintaining and correlating security logs). NTP handles this time synchronization across all devices.

**🔍 Why and how do we enumerate it?** NTP servers can leak internal network IP addresses. An older command called `monlist` returns a list of the last 600 IP addresses that synchronized their time with the server. (Attackers also abuse vulnerable NTP servers for massive DDoS amplification attacks).

**🛠️ Enumeration Tools:**
* **Ntpdc:** `ntpdc -n -c monlist <target-ip>`
* **Nmap:** `nmap -sU -p 123 --script ntp-info <target-ip>## 6. FTP (File Transfer Protocol)
* **Port:** 21

**🧠 What is it and why was it built?** FTP is one of the oldest standard network protocols. It was created simply to transfer files (uploading and downloading) between a client and a server over a local network or the internet.

**🔍 Why and how do we enumerate it?** Network administrators frequently misconfigure FTP servers by enabling **"Anonymous Login."** This allows anyone to log into the server using the username `anonymous` and a blank password. Once logged in, an attacker can download sensitive files or upload malicious scripts. Additionally, hackers use banner grabbing on port 21 to find outdated, vulnerable FTP software versions.

**🛠️ Enumeration Tools:**
* **Standard FTP Client:** `ftp <target-ip>` (Enter `anonymous` when prompted for the username to test for open access).
* **Nmap:** `nmap --script ftp-anon,ftp-bounce <target-ip>`

## 7. NFS (Network File System)
* **Port:** 2049 (Often works alongside RPC Bind/Portmap on Port 111)

**🧠 What is it and why was it built?** Just like Windows networks use SMB to share files, Linux and Unix systems use NFS. It was designed to allow users to access files and directories over a network as if they were stored locally on their own machines.

**🔍 Why and how do we enumerate it?** NFS relies on a configuration file called `/etc/exports` to define which directories are shared and which IPs can access them. If this file is misconfigured (e.g., using a wildcard `*` to allow any host), a hacker can securely "mount" the entire shared directory onto their own Linux attacking machine without needing a password, giving them full read/write access to the files.

**🛠️ Enumeration Tools:**
* **Showmount:** `showmount -e <target-ip>` (Lists all the folders being exported/shared by the target system).
* **Mount Command:** `mount -t nfs <target-ip>:/shared_folder /mnt/local_folder` (Mounts the remote file share directly onto your local machine).
* **Nmap:** `nmap -p 2049 --script nfs-showmount,nfs-ls <target-ip>`

## 8. DNS (Domain Name System)
* **Port:** 53 (UDP for standard queries, TCP for Zone Transfers)

**🧠 What is it and why was it built?** DNS is known as the phonebook of the internet. Humans remember names like `google.com`, but computers only understand IP addresses like `142.250.190.46`. DNS maps these human-readable domain names to computer-readable IP addresses.

**🔍 Why and how do we enumerate it?** The primary goal of DNS enumeration is to attempt a **"Zone Transfer (AXFR)"** attack. Zone transfers are meant to sync data between a primary and a secondary DNS server for backup purposes. However, if it is misconfigured to allow public requests, an attacker can pull the entire DNS zone file. This instantly leaks all subdomains, internal IP addresses, and mail server paths belonging to the target organization.

**🛠️ Enumeration Tools:**
* **Dig:** `dig axfr @<dns-server-ip> <domain-name>` (The standard command to attempt a zone transfer).
* **Nslookup:** `nslookup` -> `set type=any` -> `ls -d <domain-name>`
* **Dnsrecon:** `dnsrecon -d <domain-name> -t axfr`

## 9. MSRPC / RPC (Remote Procedure Call)
* **Ports:** 135 (Windows RPC), 111 (Linux RPCBind/Portmap)

**🧠 What is it and why was it built?** RPC is a system architecture that allows a program on one computer to seamlessly execute a piece of code or a service on a remote computer without needing to understand the underlying network details. Both Windows and`


---

⚠️ **Disclaimer:** This document is strictly for educational purposes and official CEH laboratory practice. Never enumerate a target without explicit, written authorization.