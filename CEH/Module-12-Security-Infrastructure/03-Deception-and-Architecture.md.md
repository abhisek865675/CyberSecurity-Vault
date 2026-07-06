# 03: Deception Technology & Network Architecture Placement 🕸️

To build an impenetrable defense, security components cannot exist in a vacuum. They must be strategically arranged in layers (Defense-in-Depth). This guide covers how to set traps using deception technology and maps out the exact industry-standard placement of security devices within an enterprise network.

---

## 📌 Table of Contents
1. [Deception Technology (Honeypots)](#1-deception-technology-honeypots)
2. [Core Network Segmentation Zones](#2-core-network-segmentation-zones)
3. [The Blueprint: Security Device Placement](#3-the-blueprint-security-device-placement)
4. [Device Placement Architecture Matrix](#4-device-placement-architecture-matrix)

---

## 1. Deception Technology (Honeypots)

A **Honeypot** is a decoy system deployed inside a network. It has no legitimate business purpose; its only job is to sit there, look valuable, and get attacked. Because no real employee should ever interact with it, **any traffic touching a honeypot is immediately classified as malicious.**

* **Low-Interaction Honeypot:** Emulates basic services (e.g., a fake SSH login prompt or web page). It consumes very few system resources but only gathers basic data like the attacker's IP, credentials attempted, and basic commands.
* **High-Interaction Honeypot:** A real operating system or application running on real hardware or virtual machines. It allows the attacker to fully log in, download tools, and move around, giving defenders deep insight into complex attack behaviors. (High risk, as it must be heavily contained so the attacker can't use it to pivot to the real network).
* **Honeynet:** A network entirely comprised of multiple honeypots. It simulates an entire corporate subnet (complete with fake active domain controllers, file shares, and routers).
* **Honeytoken:** Decoy data mixed in with real corporate files. Examples include fake database entries, a bogus Excel file named `Executive_Salaries.xlsx`, or fake API keys. If an attacker steals and attempts to use a honeytoken, an immediate high-priority alert triggers.

---

## 2. Core Network Segmentation Zones

You cannot protect a network if every device sits in the same flat broadcast domain. Enterprises break networks into three distinct trust zones:

1. **The Public Network (The Internet):** Completely untrusted. Anyone can pass traffic here.
2. **The DMZ (Demilitarized Zone):** A semi-trusted isolation buffer zone. This houses servers that *must* face the public internet to do their jobs (e.g., public web servers, external mail proxies, and DNS servers).
3. **The Internal Corporate Network (LAN):** Completely trusted (in theory). This houses sensitive domain controllers, internal databases, HR systems, and employee workstations. 

---

## 3. The Blueprint: Security Device Placement

Here is the exact linear sequence of how data moves from the internet down to an endpoint, and where each security appliance sits to intercept it.

```text
       [ THE INTERNET (Untrusted) ]
                    │
                    ▼
            [ 1. Edge Router ]
                    │
                    ▼
       [ 2. External Firewall (NGFW) ] ───► [ DMZ Zone ]
                    │                        (Web Server, Mail Gateway)
                    ▼
       [ 3. Inline IPS (Prevention) ]
                    │
                    ▼
            [ Core Switch ] ═════════════► [ 4. Passive IDS ]
                    │                        (SPAN/Mirror Port Monitor)
                    ▼
     [ INTERNAL CORPORATE LAN ]
                    │
                    ├─► [ 5. Endpoint Host ] (Running EDR / XDR Agent)
                    │
                    └─► [ 6. Honeypot Subnet ] (Isolated Trap Zone)
```


### 🧱 Architectural Step-by-Step Breakdown:

1. **Edge Router:** The very front door of the company. It connects directly to the Internet Service Provider (ISP). It handles high-level internet traffic routing and performs basic infrastructure protections (like dropping obviously spoofed IP spaces).
    
2. **External Firewall (NGFW):** Placed right behind the Edge Router. It acts as the primary gatekeeper. It splits incoming traffic two ways: one path goes directly to the **DMZ** to let customers browse the company website, while blocking all direct traffic from the internet into the internal network.
    
3. **Inline IPS:** Placed _behind_ the external firewall but _before_ the internal network switches. Since the firewall filters traffic based on rules/applications, the IPS performs real-time Deep Packet Inspection to actively kill malicious execution streams, exploits, and payload streams that managed to bypass the firewall rules.
    
4. **Passive IDS:** Connected out-of-band to a **SPAN or Mirror Port** on the core internal network switches. It passively analyzes copies of internal corporate traffic traffic to generate background telemetry logs, checking for lateral movement or configuration errors without slowing down live application traffic.
    
5. **EDR / XDR Agents:** Installed directly at the OS level on every individual laptop, workstation, and domain controller inside the LAN. If an attacker slips past the external firewall and IPS (e.g., via a compromised USB stick or a phishing email), the EDR agent catches the threat when the malware tries to run on the endpoint host.
    
6. **SIEM / SOAR Platform:** Sits securely deep within the internal infrastructure. It doesn't process active network traffic flow. Instead, the Firewall, IPS, IDS, and EDR agents all stream their event logs up to the SIEM, creating a single centralized control room for the entire defensive architecture.
    

## 4. Device Placement Architecture Matrix

| **Device / Appliance** | **Positioning Type**  | **Exact Logical Location**                              | **Core Architectural Purpose**                                                                     |
| ---------------------- | --------------------- | ------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **External Firewall**  | Inline (In-band)      | Between Edge Router and DMZ/LAN                         | Separates public web traffic from critical internal enterprise assets.                             |
| **IPS**                | Inline (In-band)      | Behind External Firewall, ahead of Core Switch          | Actively drops exploit payloads and web attacks before they reach internal hosts.                  |
| **IDS**                | Passive (Out-of-band) | Attached to Core Switch SPAN / Mirror Port              | Generates background alerts for suspicious lateral movement across internal subnets.               |
| **EDR / XDR**          | Host-Based Agent      | Installed inside the OS Kernel of end-user machines     | Final layer of defense tracking process changes, file alterations, and fileless malware execution. |
| **Honeypot**           | Host Target           | Isolated inside its own highly restricted internal VLAN | Acts as an early warning trap to detect attackers attempting internal reconnaissance.              |

---
