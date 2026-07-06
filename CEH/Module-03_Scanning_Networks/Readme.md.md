# Module 03: Scanning Networks 🔍

Welcome to the **Scanning Networks** module notes. Following Reconnaissance (Information Gathering), this is the second most crucial phase in ethical hacking. In this module, we will learn how to scan a target network to identify active hosts, open ports, running services, and potential vulnerabilities.

---

## 📖 Overview & Core Concepts
The primary objective of Network Scanning is to create a live map of the target network. This phase focuses on three main objectives:
* **Host Discovery:** Determining which devices (hosts) are currently active and live on the network.
* **Port Scanning:** Identifying open ports on active hosts and figuring out which services are running on them.
* **Vulnerability Scanning:** Finding outdated services or security loopholes (vulnerabilities) within the target systems.

> 💡 **Important Concept:** To fully grasp the concepts in this module, a solid understanding of the **TCP 3-Way Handshake** (`SYN` -> `SYN-ACK` -> `ACK`) and TCP Flags (`SYN`, `ACK`, `FIN`, `RST`, `PSH`, `URG`) is essential.

---

## ⚡ Scanning Techniques
We have covered the following scanning types in depth within this module:
* **Host Discovery Techniques:** ICMP Ping, ARP Ping, and TCP Ping sweeps.
* **TCP Connect / Full Open Scan:** Completes the full connection with the target (highly reliable but easy to detect).
* **SYN / Stealth / Half-Open Scan:** Does not complete the 3-way handshake, making it much harder to detect in standard logs.
* **Inverse TCP Scans:** Null Scan, Xmas Scan, and FIN Scan (often used to bypass and map firewall rules).
* **UDP Scanning:** Used to scan connectionless ports and services.

---

## 🛠️ Tools Covered
The theoretical notes and labs in this module involve the practical use of the following tools:
* 🟢 **Nmap (Network Mapper):** The industry-standard tool for network discovery and security auditing.
* 🔵 **Hping3:** A command-line oriented TCP/IP packet assembler and analyzer, great for testing firewall rules.
* 🟡 **Masscan:** An extremely fast network scanner capable of scanning the entire internet in minutes.
* 🟠 **Zenmap:** The official Graphical User Interface (GUI) for Nmap.

---

## 🛡️ Evasion & Countermeasures
Scanning is only half the battle. This module also covers how to stay undetected and how to defend against scans:
* **Firewall/IDS Evasion:** Techniques like Fragmentation (`-f`), Decoys (`-D`), and Spoofing to hide scanning traffic.
* **Defenses:** How to configure firewalls to block malicious scans, understanding Port Knocking, and setting up Intrusion Detection Systems (IDS).

---
