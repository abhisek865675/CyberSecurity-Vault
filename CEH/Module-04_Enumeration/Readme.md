# Module 04: Enumeration 🕵️‍♂️

Welcome to the **Enumeration** module notes. After discovering live hosts and open ports in the Scanning phase, Enumeration is the process of extracting actionable information from those services. This involves creating active connections to the target system to gather details like usernames, machine names, network resources, and shares.

---

## 📌 Table of Contents
1. [Overview & Core Concepts](#-overview--core-concepts)
2. [Key Protocols to Enumerate](#-key-protocols-to-enumerate)
3. [Tools Covered](#%EF%B8%8F-tools-covered)
4. [🛡️ Countermeasures](#%EF%B8%8F-countermeasures)

---

## 📖 Overview & Core Concepts
Enumeration is an **active** phase. Unlike passive footprinting, enumeration requires direct interaction with the target's network and services, making it highly visible to Intrusion Detection Systems (IDS). 

**What are we looking for?**
* Usernames and Group names
* Machine names and OS details
* Network shares and accessible files
* Routing tables and SNMP data
* Application-specific details (like DNS zones or database tables)

---

## 🔑 Key Protocols to Enumerate
In this module, we break down enumeration by the protocol/service running on the target's open ports:

* **NetBIOS / SMB (Ports 139, 445):** Extracting file shares, null sessions, and user lists from Windows environments.
* **SNMP (Ports 161, 162 UDP):** Querying routers and switches for network configurations, device data, and routing tables.
* **LDAP (Port 389):** Querying Active Directory for user hierarchies and network structure.
* **NTP (Port 123 UDP):** Extracting time server data and connected client IPs.
* **SMTP (Port 25):** Verifying email addresses and usernames using `VRFY`, `EXPN`, and `RCPT TO` commands.

---

## 🛠️ Tools Covered
The theoretical notes and labs in this module involve the practical use of the following tools:
* 🟢 **Enum4linux:** The ultimate tool for enumerating information from Windows and Samba systems.
* 🔵 **Snmpwalk / Snmpcheck:** Used to query SNMP-enabled devices using MIBs (Management Information Bases).
* 🟡 **Smbclient:** A command-line tool to interact with SMB shares (like a localized FTP client).
* 🟠 **Nmap Scripting Engine (NSE):** Using specific scripts (e.g., `smb-enum-users`, `smtp-enum-users`) for automated enumeration.

---

## 🛡️ Countermeasures
How to secure systems against enumeration attacks:
* **SMB:** Disable SMBv1, restrict Null Sessions (`RestrictAnonymous=1`), and enforce strong share permissions.
* **SNMP:** Upgrade to SNMPv3 (which includes encryption and authentication), or change default community strings (e.g., changing "public" or "private" to complex passwords).
* **SMTP:** Disable or restrict the `VRFY` and `EXPN` commands on the mail server.

---

⚠️ **Disclaimer:** All notes and lab write-ups in this repository are strictly for educational and ethical hacking purposes. Performing enumeration against any system without explicit, written permission is illegal. Always practice in a controlled, authorized environment.