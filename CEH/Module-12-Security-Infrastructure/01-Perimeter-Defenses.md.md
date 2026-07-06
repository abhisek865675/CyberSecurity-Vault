#  Perimeter Defenses (Firewalls, IDS, IPS) 🧱

The perimeter is the boundary between the untrusted, wild internet and the trusted, internal corporate network. Defending this boundary relies on filtering traffic and inspecting packets for known malicious signatures.


---

## 📌 Table of Contents
1. [Firewalls: The Gatekeepers](#1-firewalls-the-gatekeepers)
2. [Intrusion Detection Systems (IDS): The Security Cameras](#2-intrusion-detection-systems-ids-the-security-cameras)
3. [Intrusion Prevention Systems (IPS): The Armed Guards](#3-intrusion-prevention-systems-ips-the-armed-guards)
4. [Detection Methodologies](#4-detection-methodologies)

---

## 1. Firewalls: The Gatekeepers

A firewall enforces an Access Control List (ACL). It sits **inline** (directly in the path of traffic) and decides what gets in and what stays out based on specific rules. Firewalls have evolved through three major generations:

### A. Stateless (Packet Filtering) - Layer 3 & 4
* **How it works:** Inspects every packet individually in isolation. It only looks at the Source IP, Destination IP, Protocol, and Port number.
* **The Weakness:** It has no memory. It doesn't know if a packet is part of a legitimate ongoing conversation or an attacker throwing random packets at the network. (Highly vulnerable to spoofing).

### B. Stateful Inspection - Layer 4
* **How it works:** It maintains a "State Table" in its memory. It tracks the entire TCP 3-way handshake. If an external IP tries to send data inside without first establishing a valid handshake, the firewall drops it.
* **The Weakness:** It cannot see *inside* the packet. If an attacker sends a malicious SQL Injection payload over an allowed port (like Port 443 for HTTPS), the stateful firewall lets it right through.

### C. Next-Generation Firewalls (NGFW) - Layer 7
* **How it works:** NGFWs feature **Deep Packet Inspection (DPI)**. They look past the IP addresses and ports to examine the actual payload of the traffic. They understand applications—meaning they can block a user from accessing Facebook Chat while still allowing them to load the main Facebook feed.

---

## 2. Intrusion Detection Systems (IDS): The Security Cameras

An IDS is a passive monitoring device. It does not sit inline; instead, it is connected to a **SPAN port** or **Mirror port** on a switch. 

* **The Mechanism:** The switch sends a copy of all network traffic to the IDS. The IDS analyzes this traffic against a database of known attacks.
* **The Action:** If it sees a malicious packet, it generates an alert and logs it to a central server (SIEM). 
* **The Limitation:** Because it is passive and out-of-band, **an IDS cannot stop an attack**. By the time the IDS triggers the alarm, the malicious packet has already reached its destination. 

---

## 3. Intrusion Prevention Systems (IPS): The Armed Guards

An IPS is the active evolution of an IDS. It sits **inline** with the traffic flow (usually right behind the firewall).

* **The Mechanism:** All traffic must physically pass *through* the IPS before it reaches the internal network.
* **The Action:** If the IPS detects a malicious packet, it can actively intervene. It can drop the packet, block the attacker's IP address, or reset the TCP connection (`TCP RST`).
* **The Risk:** If an IPS has poorly tuned rules, it can generate "False Positives" and accidentally block legitimate business traffic, causing a self-inflicted Denial of Service.

---

## 4. Detection Methodologies

Both IDS and IPS rely on two primary ways to catch bad guys:

| Methodology | How it Works | Pros / Cons |
| :--- | :--- | :--- |
| **Signature-Based** | Compares traffic against a database of known malicious hashes, byte sequences, or attack patterns (like antivirus). | **Pros:** Very fast, zero false positives.<br>**Cons:** Cannot detect new, "Zero-Day" attacks. |
| **Anomaly-Based (Heuristic)** | Establishes a baseline of "normal" network behavior. Triggers an alert if traffic deviates from that baseline (e.g., an employee downloading 50GB of data at 3 AM). | **Pros:** Can catch brand new, unseen attacks.<br>**Cons:** Slower, generates many false positives if not tuned properly. |

---

> **💡 Red Team Perspective (Evasion):**
> Attackers bypass these defenses using techniques like **Packet Fragmentation** (breaking a malicious payload into tiny pieces so the IDS doesn't recognize the signature until it reassembles at the target) or **Encryption** (hiding the payload inside SSL/TLS so the firewall cannot inspect it).