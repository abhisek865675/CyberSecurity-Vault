# 01: DoS vs. DDoS Concepts 💥

While the goal of both attacks is the same—crashing a system or network—the execution and scale are vastly different. Understanding this difference is critical for designing proper defensive architectures.

---

## 📌 Table of Contents
1. [DoS vs. DDoS: The Core Difference](#-dos-vs-ddos-the-core-difference)
2. [The Botnet Architecture](#-the-botnet-architecture)
3. [Reflection and Amplification](#-reflection-and-amplification)
4. [The 3 Categories of DoS Attacks](#-the-3-categories-of-dos-attacks)

---

## 1. DoS vs. DDoS: The Core Difference

### DoS (Denial of Service)
A DoS attack originates from a **single source** (one computer or one internet connection). 
* **The Problem:** It is relatively easy to defend against. A firewall or Intrusion Prevention System (IPS) can quickly identify the single attacking IP address and block it. 
* **Analogy:** One person standing at the front door of a store, blocking customers from coming in. The security guard can easily remove that one person.

### DDoS (Distributed Denial of Service)
A DDoS attack originates from **multiple sources** (often thousands or millions of devices) simultaneously attacking the target.
* **The Problem:** You cannot simply "block the IP" because there are thousands of legitimate-looking IPs flooding you from all over the world. If you block them all, you also block your real customers.
* **Analogy:** 10,000 people rushing the front door of a store at the exact same time. The security guard is completely overwhelmed.

---

## 2. The Botnet Architecture
To launch a DDoS attack, an attacker needs an army. This army is called a **Botnet**.

* **Bots (Zombies):** These are compromised computers, smartphones, smart TVs, or IoT devices (like security cameras). The legitimate owners usually have no idea their device is infected.
* **C2 Server (Command and Control):** The centralized server used by the attacker to issue commands to the botnet. 
* **Bot Herder:** The attacker who controls the botnet. They send a single command to the C2 server ("Attack IP 192.168.1.50"), and the C2 server relays that command to 100,000 bots simultaneously.

---

## 3. Reflection and Amplification
This is a devastating technique where the attacker doesn't attack the victim directly. Instead, they use third-party servers to "bounce" and "magnify" the attack.

1. **Spoofing:** The attacker sends a request to a legitimate server (e.g., a DNS server), but they forge the *source IP address* to look like the victim's IP.
2. **Reflection:** The DNS server processes the request and sends the reply to the spoofed IP (the victim), not the attacker. 
3. **Amplification (The Multiplier):** The attacker sends a tiny request (e.g., 60 bytes), but the DNS server's reply is massive (e.g., 3,000 bytes). The attacker has just amplified their attack power by 50x without using their own bandwidth.

---

## 4. The 3 Categories of DoS Attacks

When building defenses, we categorize attacks based on *how* they overwhelm the target:

| Category | Target | Description | Example |
| :--- | :--- | :--- | :--- |
| **Volumetric** | Bandwidth | Floods the network pipe so no legitimate traffic can fit through. Measured in Bits Per Second (Bps). | UDP Flood, ICMP Flood |
| **Protocol** | Infrastructure | Exhausts the state tables of firewalls, load balancers, or servers by leaving connections "half-open." | SYN Flood, Ping of Death |
| **Application (Layer 7)** | Web Server | Sends complex, legitimate-looking HTTP requests that force the server's CPU/Database to work exhaustively until it crashes. | Slowloris, HTTP GET Flood |

---

> **💡 Pro Tip for Defenders:**
> IoT devices (like cheap internet-connected cameras and smart plugs) are currently the biggest contributors to global Botnets (e.g., the Mirai Botnet). They often have default passwords (`admin/admin`) that cannot be changed, making them incredibly easy for attackers to compromise and enslave.