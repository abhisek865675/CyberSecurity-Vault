# ARP Spoofing and MITM ⚔️

ARP (Address Resolution Protocol) is the glue of the local network. It maps IP addresses to MAC addresses. Unfortunately, ARP was designed in an era of trust—it is completely stateless. This means a device will accept an ARP reply even if it never sent an ARP request. This fundamental design flaw makes ARP Spoofing (or ARP Poisoning) possible.


---

## 📌 Table of Contents
1. [How ARP Works (The Foundation)](#-how-arp-works-the-foundation)
2. [The MITM Attack Workflow](#-the-mitm-attack-workflow)
3. [Tools of the Trade](#-tools-of-the-trade)
4. [Countermeasures](#-countermeasures)

---

## 1. How ARP Works (The Foundation)
When Computer A wants to talk to Computer B, it sends a broadcast asking: *"Who has IP 192.168.1.5?"*
Computer B replies: *"I do, my MAC address is AA:BB:CC:DD:EE:FF."*
Computer A updates its **ARP Cache** (a local table) and starts talking.

**The Flaw:** If an attacker sends a fake "I am 192.168.1.5" message to Computer A, Computer A will overwrite its cache with the *attacker's* MAC address. Now, all traffic meant for B goes to the Attacker.

---

## 2. The MITM Attack Workflow
To perform a full Man-in-the-Middle attack, you must poison *two* caches: the Victim's and the Gateway's (Router).



1. **Poisoning:** You send a forged ARP reply to the **Victim**, telling them: *"I am the Gateway."*
2. **Double Poisoning:** You send a forged ARP reply to the **Gateway**, telling them: *"I am the Victim."*
3. **Interception:** Traffic flows from Victim -> Attacker -> Gateway and back from Gateway -> Attacker -> Victim.
4. **Sniffing:** Because the traffic is passing through your machine, you can now use `Wireshark` or `Ettercap` to view all the unencrypted data, passwords, and URLs flowing in both directions.

---

## 3. Tools of the Trade
Don't write these scripts from scratch; use proven, professional tools.

* **Arpspoof (dSniff suite):** The simplest tool.
    * *Command:* `arpspoof -i eth0 -t [Victim_IP] [Gateway_IP]`
* **Ettercap:** A powerful graphical and command-line tool. It automates the poisoning, sniffing, and even allows for "Filters" (e.g., injecting malicious scripts into victim web pages on the fly).
* **Bettercap:** The modern successor to Ettercap. It features a built-in web dashboard, better support for IPv6, and advanced credential sniffing modules.
    * *Command:* `bettercap -iface eth0` -> `arp.spoof on` -> `net.sniff on`

---

## 4. Countermeasures
How do network administrators stop this?

* **Static ARP Tables:** Manually binding IP addresses to MAC addresses on critical machines (not scalable, but secure).
* **Dynamic ARP Inspection (DAI):** A feature on high-end enterprise switches that validates ARP packets. If an ARP reply doesn't match the valid "binding database" on the switch, the packet is dropped.
* **Port Security:** Limiting the number of MAC addresses allowed on a single switch port.
* **Encryption (The Ultimate Defense):** If the data is encrypted (HTTPS, SSH, VPN), the attacker can successfully intercept the traffic, but they will be unable to read the contents. **Encryption renders sniffing useless.**

---

> **💡 Pro Tip for CEH:**
> ARP Spoofing is loud. It floods the network with packets. An Intrusion Detection System (IDS) like **Snort** or **Suricata** will easily flag "ARP Cache Poisoning Detected" if you run this in a production environment. Always test this in your controlled virtual lab.

---

⚠️ **Ethical Disclaimer:** This technique causes "Denial of Service" if not configured correctly (if the attacker doesn't forward the packets properly, the victim loses internet). Never perform MITM attacks on a production network or public Wi-Fi.