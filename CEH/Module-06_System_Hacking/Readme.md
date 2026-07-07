# Module 06: System Hacking 💻

Welcome to the **System Hacking** module notes. This is the core phase of ethical hacking. After gathering extensive data and identifying vulnerabilities in the previous modules, this phase is all about actually exploiting those vulnerabilities to compromise the target system. 

---

## 📌 Table of Contents
1. [Overview & The CEH Methodology](#-overview--the-ceh-methodology)
2. [Phase 1: Gaining Access](#-phase-1-gaining-access)
3. [Phase 2: Escalating Privileges](#-phase-2-escalating-privileges)
4. [Phase 3: Maintaining Access](#-phase-3-maintaining-access)
5. [Phase 4: Clearing Tracks](#-phase-4-clearing-tracks)
6. [Tools Covered](#%EF%B8%8F-tools-covered)
7. [📂 Lab Directory (Practicals)](#-lab-directory-practicals)

---

## 📖 Overview & The CEH Methodology
System hacking is not a single action; it is a structured step-by-step process. According to the EC-Council CEH methodology, System Hacking consists of four distinct phases that an attacker follows once a vulnerability is found.

---

## 🚪 Phase 1: Gaining Access
This is the initial break-in. The goal is to bypass security controls and get a shell (command-line access) on the target machine.
* **Techniques Used:** * Exploiting software vulnerabilities (e.g., Buffer Overflows).
  * Password Cracking (Offline cracking of hashes or Online brute-forcing).
  * Social Engineering & Phishing (Tricking users into running malicious payloads).

---

## ⬆️ Phase 2: Escalating Privileges
Once you gain initial access, you usually land as a low-level, standard user. The goal here is to become the administrator (`root` in Linux or `SYSTEM` in Windows).
* **Vertical Escalation:** Moving from a standard user to an Admin/Root user.
* **Horizontal Escalation:** Moving from one standard user to another standard user (to access their specific files).
* **Techniques Used:** Exploiting kernel flaws, abusing misconfigured permissions (SUID in Linux), or extracting cleartext passwords from memory (using tools like Mimikatz).

---

## 🕵️‍♂️ Phase 3: Maintaining Access
If the target reboots or the user changes their password, you don't want to lose your hard-earned access. You need to create a permanent entry point.
* **Techniques Used:**
  * Planting Backdoors and Trojans.
  * Creating rogue admin accounts.
  * Installing Rootkits (which hide themselves deep in the operating system).
  * Using Scheduled Tasks or Cron Jobs to automatically connect back to the attacker.

---

## 🧹 Phase 4: Clearing Tracks
An ethical hacker must know how malicious hackers hide their presence to avoid detection by Digital Forensics and Incident Response (DFIR) teams.
* **Techniques Used:**
  * Deleting or altering system logs (e.g., Windows Event Logs, `/var/log` in Linux).
  * Hiding files using Alternate Data Streams (ADS) in Windows.
  * Disabling antivirus and auditing services.

---

## 🛠️ Tools Covered
This module introduces some of the most powerful and famous tools in the cybersecurity industry:
* 🟢 **Metasploit Framework:** The ultimate exploitation framework used to develop, test, and execute exploit code against a remote target.
* 🔵 **Mimikatz:** A post-exploitation tool used to extract plaintexts passwords, hashes, and PIN codes from Windows memory.
* 🟡 **John the Ripper / Hashcat:** Industry-standard tools for advanced offline password cracking.
* 🟠 **Netcat (nc):** The "Swiss Army Knife" of networking, heavily used to catch reverse shells and bind shells.

---

⚠️ **Disclaimer:** All notes, exploit codes, and lab write-ups in this repository are strictly for educational and ethical hacking purposes. Unauthorized exploitation of systems is a severe cybercrime. Always practice in a controlled, authorized environment like HackTheBox, TryHackMe, or local Virtual Machines.