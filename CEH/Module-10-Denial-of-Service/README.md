# Module 10: Denial of Service (DoS & DDoS) 💥

Denial of Service (DoS) attacks do not try to steal data or bypass security controls. Instead, their sole objective is to take a system offline, preventing legitimate users from accessing a service, website, or network.

---

## 📌 Module Overview
This module explores how attackers overwhelm systems with traffic or exploit protocol flaws to crash servers.

1. **Concepts:** The difference between a standard DoS and a Distributed Denial of Service (DDoS) attack utilizing global botnets.
2. **Techniques:** How attackers abuse TCP, UDP, and ICMP protocols (Volumetric vs. Application-layer attacks).
3. **Mitigation:** How to survive an attack using load balancing, Web Application Firewalls (WAFs), and traffic scrubbing.

---

## 🧠 The Core Philosophy: Asymmetry
A successful DoS attack relies on **Asymmetry**. The attacker wants to use a very small amount of resources to force the target server to consume a massive amount of resources (CPU, Memory, or Bandwidth). 

* **Volumetric Attacks:** "I have a bigger internet pipe than you, so I will drown your router in junk data."
* **Application/Protocol Attacks:** "I will send you a tiny, complex request that takes your CPU 10 seconds to process. I will send 1,000 of these per second until you crash."

---

## 🏗️ The Threat Landscape
Modern DDoS attacks are rarely launched from a single computer. They are launched via **Botnets**—networks of compromised IoT devices, routers, and PCs controlled by a single Command & Control (C2) server. Attackers often rent these out on the dark web as "DDoS-for-Hire" services (Stresser services).

---

⚠️ **Ethical & Legal Disclaimer:**
Denial of Service attacks are highly illegal and can cause massive financial damage to a business. **Never** test DoS tools against a network, website, or IP address you do not explicitly own. Even in authorized penetration testing, DoS is usually strictly excluded from the Rules of Engagement (RoE) because it can take down production environments.