# 04: Advanced Network Attacks: MAC Flooding & DNS Poisoning ⚠️

Moving beyond simple MITM attacks, we look at exploits that target network infrastructure and core internet protocols. These attacks are designed to disrupt the "trusted" architecture of modern networks.

---

## 📌 Table of Contents
1. [MAC Flooding (CAM Table Overflow)](#-mac-flooding-cam-table-overflow)
2. [DNS Poisoning (DNS Spoofing)](#-dns-poisoning-dns-spoofing)
3. [Defense Summary](#-defense-summary)

---

## 1. MAC Flooding (CAM Table Overflow)
Switches are "smart" because they keep a **CAM (Content Addressable Memory) table**, which maps MAC addresses to specific physical ports. 

### The Mechanism
An attacker sends thousands of Ethernet frames with spoofed, random source MAC addresses to the switch. 
1. The switch, trying to be helpful, updates its CAM table for every single fake MAC address it sees.
2. The CAM table has a limited amount of memory. Once it hits its capacity, it cannot store any new valid MAC-to-port mappings.
3. **The Result:** The switch enters a "Fail-Open" mode (often called *hub mode*). Because it can no longer map MACs, it broadcasts all incoming traffic to every port on the switch. 
4. **The Goal:** Once the switch is broadcasting everything, the attacker can use a simple sniffer (like Wireshark) to capture *all* traffic on the network.



---

## 2. DNS Poisoning (DNS Spoofing)
DNS is the phonebook of the internet. When you type `google.com`, your computer asks a DNS server for the IP address. DNS typically uses **UDP**, which is connectionless and stateless.

### The Mechanism (The Race Condition)
DNS poisoning relies on a race condition. When a victim asks for a domain's IP:
1. The Attacker observes the DNS request.
2. The Attacker immediately sends a forged DNS reply to the victim, claiming that `google.com` resides at the attacker's malicious IP address (e.g., a phishing page).
3. If the attacker's packet arrives before the legitimate DNS server's packet, the victim's machine accepts the fake IP and caches it.
4. **The Goal:** Redirecting users to malicious websites or phishing portals without them knowing.



---

## 🛡️ Defense Summary

| Attack | Vulnerability | Defensive Countermeasure |
| :--- | :--- | :--- |
| **MAC Flooding** | Finite CAM table memory | **Port Security:** Limit the number of MAC addresses allowed per port on the switch. |
| **DNS Poisoning** | Stateless UDP protocol | **DNSSEC:** Cryptographically signs DNS data to ensure integrity. |
| **DNS Poisoning** | Open resolvers | **Hardened Resolvers:** Configure DNS servers to ignore unsolicited responses. |

---

> **💡 Key Takeaway for Professionals:**
> MAC Flooding is a "dumb" attack—it's loud and easy for modern switches to detect with Port Security. DNS Poisoning is more "clever" and is often used in sophisticated phishing campaigns. Always remember: **Encryption (HTTPS) doesn't stop the DNS redirection, but it does stop the attacker from reading your data once you arrive at the fake site.**

---

⚠️ **Ethical Disclaimer:** These attacks can easily destabilize a network, causing DoS (Denial of Service) for legitimate users. MAC Flooding may cause a switch to crash or enter a fail-safe state. Only perform these actions in a strictly isolated, lab-controlled environment.