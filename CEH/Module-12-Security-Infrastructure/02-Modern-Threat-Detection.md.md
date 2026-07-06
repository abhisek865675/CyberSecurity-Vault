# 02: Modern Threat Detection (EDR, XDR, and SIEM) 🛡️

Legacy protection focused entirely on the network perimeter. However, in modern environments—where users work remotely, access SaaS applications, and use cloud infrastructure—the traditional perimeter is gone. Modern defense requires active monitoring at the application, telemetry, and host kernel level.

<Image src="image_agent_tag_1910469912418039774" alt="Diagram showing data collection from on-premise infrastructure, cloud, and SaaS apps flowing into a centralized SIEM, which integrates with alerting, orchestration, threat intelligence, and case management." caption="Modern centralized detection ecosystem architecture" />

---

## 📌 Table of Contents
1. [Beyond Legacy Antivirus (EPP)](#1-beyond-legacy-antivirus-epp)
2. [EDR: Endpoint Detection & Response](#2-edr-endpoint-detection--response)
3. [XDR: Extended Detection & Response](#3-xdr-extended-detection--response)
4. [SIEM: The Central Nervous System](#4-siem-the-central-nervous-system)

---

## 1. Beyond Legacy Antivirus (EPP)

Traditional Antivirus—now known as an **Endpoint Protection Platform (EPP)**—is purely preventive. It works like a passport control officer checking an expiration date:
* It looks for known malicious file signatures (hashes).
* If a file matches a signature in its database, it blocks it.

### The Failure Point
If an attacker changes a single byte of code in their malware, the file hash changes entirely. Legacy antivirus will fail to see it. Furthermore, attackers now use **Fileless Malware** (e.g., abusing legitimate built-in tools like PowerShell or `wmic` directly in memory). Because no file is saved to the hard drive, static signature scanners see nothing.

---

## 2. EDR: Endpoint Detection & Response

EDR does not just look at static files; it records active behavior. Think of EDR as a flight data recorder ("black box") for a computer host or server.

<Image src="image_agent_tag_1910469912418040449" alt="Diagram showing the EDR loop: 1. Continuous Monitoring, 2. Threat Detection, 3. Analysis & Investigation, 4. Response & Containment, 5. Remediation & Recovery, 6. Post-Incident Review & Tuning." caption="The continuous EDR operations workflow cycle" />

### How it Captures Attacks
EDR hooks deep into the operating system kernel to monitor:
* Process creations (e.g., Why did Microsoft Word suddenly launch a Command Prompt?)
* Network connections made by local applications.
* Registry alterations and memory injections.

If an EDR detects a suspicious behavioral pattern (Heuristics), it flags an alert and gives analysts remote capability to isolate the machine from the network instantly, preventing lateral movement.

---

## 3. XDR: Extended Detection & Response

While EDR is powerful, it has tunnel vision—it can only see what happens directly on the endpoint host. If an attacker uses a compromised cloud account to alter an email inbox rule and exfiltrate data, the local laptop's EDR has no visibility. 

**XDR** breaks down these silos by gathering data across multiple security control layers:

[Endpoint Data] + [Network Logs] + [Cloud / SaaS Activity] + [Identity Provider Logs] ---> Centralized XDR Engine

### The Power of Correlation XDR uses machine learning to connect dots that look harmless in isolation.

**Isolated Event 1:* An employee logs in from a new IP address (Identity layer). 
**Isolated Event 2:* A production database exports an uncommonly large CSV file (Database layer). 
**Isolated Event 3:* An external SSH connection leaves the network (Firewall layer).

##### XDR correlates these separate timestamps into a single cohesive timeline, recognizing an active data exfiltration breach in progress. 

---

## 4. SIEM: The Central Nervous System

**Security Information and Event Management (SIEM)** tools act as the central log aggregator for an entire enterprise network.

* **Log Collection:** It pulls text-based log files from every firewall, domain controller, Linux server, and database in the company.
* **Normalization & Correlation:** It translates different log formats into a single readable standard, looking for specific logic strings (e.g., 50 failed login attempts on a server followed by 1 successful login = Brute Force success).
* **SOAR Integration:** Modern SIEM platforms are paired with **SOAR (Security Orchestration, Automation, and Response)**. If the SIEM correlates a high-severity alert, the SOAR engine can execute automated playbooks—like automatically blocking an attacker's IP at the perimeter firewall without waiting for a human analyst to wake up.

---

## 🛠️ Infrastructure Detection Capabilities Matrix

| System Type | Primary Focus | Main Strength | Biggest Blindspot |
| :--- | :--- | :--- | :--- |
| **Legacy AV / EPP** | Known File Hashes | Instant prevention of old malware | Zero-Day exploits / Fileless attacks |
| **EDR** | Endpoint Host Activity | Deep visibility into process execution | No visibility into network or cloud layers |
| **XDR** | Multi-Layer Telemetry | Correlates cloud, host, identity, and network data | Complex deployment and fine-tuning |
| **SIEM** | Centralized Log Storage | Long-term log storage, compliance auditing | Requires manually written parsing rules |

---

> **💡 Red Team Perspective:**
> EDR is the primary enemy of a modern penetration tester. Bypassing an EDR requires sophisticated evasion techniques, such as **Direct Syscalls** (bypassing the hooked Windows API functions by talking straight to the kernel) or **Unhooking** (reloading a clean copy of system DLLs into memory to clear the EDR's tracking sensors).


---
