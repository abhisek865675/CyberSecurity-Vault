# Sniffing Foundations 📡

Packet sniffing is the practice of intercepting and capturing data packets traversing a network. At its core, it is about visibility. By analyzing packets, an attacker can extract unencrypted passwords, view sensitive files, or map out the architecture of an internal network.

---

## 📌 Table of Contents
1. [Hubs vs. Switches (The "Why")](#-hubs-vs-switches-the-why)
2. [Promiscuous Mode](#-promiscuous-mode)
3. [Passive vs. Active Sniffing](#-passive-vs-active-sniffing)
4. [Sniffing Countermeasures](#-sniffing-countermeasures)

---

## 🏗️ Hubs vs. Switches (The "Why")

To understand sniffing, you must understand how data moves through hardware.

* **Hubs (The Easy Sniff):** Hubs are "dumb" devices. When a hub receives a packet, it broadcasts it to *every* port. If you are plugged into a hub, your Network Interface Card (NIC) sees everything happening on the network, even if it wasn't meant for you. **Sniffing on a hub is naturally passive.**
* **Switches (The Challenge):** Switches are "smart." They maintain a **MAC Address Table** (Content Addressable Memory or CAM Table). They only send data to the specific port associated with the destination MAC address. 
    * *Reality Check:* You cannot simply "listen" on a modern switched network. You have to use **Active Sniffing** techniques (like ARP Spoofing) to trick the switch into sending you traffic that wasn't meant for you.

---

## 🕶️ Promiscuous Mode
Under normal conditions, a NIC only processes packets addressed specifically to its own MAC address or broadcast packets. 

* **Promiscuous Mode:** When a sniffer runs, it forces the NIC into "Promiscuous Mode." This tells the card to stop ignoring packets meant for other devices and instead pass *every* packet it sees on the wire to the CPU for analysis.
* *Note:* If you see "Promiscuous Mode: Yes" in your tool, you are successfully sniffing the wire.

---

## ⚔️ Passive vs. Active Sniffing

| Type | How it Works | Detection Risk |
| :--- | :--- | :--- |
| **Passive Sniffing** | Simply listening to traffic on a hub or a SPAN/Mirror port. | **Low:** The sniffer emits no traffic, making it invisible to detection tools. |
| **Active Sniffing** | Performing ARP Spoofing, MAC Flooding, or DHCP Spoofing to divert traffic through your machine. | **High:** The attack involves sending malformed packets that can be detected by IDS/IPS. |

---

## 🕵️ How to Sniff a Switched Network (The Professional Way)
Since switches prevent basic sniffing, we use these advanced techniques:

1. **MAC Flooding:** Bombarding the switch with fake MAC addresses until its CAM table overflows. Once full, the switch often enters "fail-open" mode and acts like a hub, broadcasting all traffic to all ports.
2. **ARP Spoofing:** Sending fake ARP replies to the victim and the gateway, claiming *you* are the other party. Traffic begins flowing through your machine (Man-in-the-Middle).
3. **SPAN/Port Mirroring:** If you have access to the switch configuration, you can enable a "Mirror Port." The switch automatically copies all traffic from other ports to your port. This is the most professional and undetectable way to sniff.

---

> **💡 Key Takeaway:**
> On a modern network, you are rarely a passive observer. You are an active participant. Always remember: **The more noise you make (Active Sniffing), the more likely you are to be caught.**

---

⚠️ **Warning:** Always verify your lab environment. Attempting these techniques on enterprise equipment without authorization can trigger an automated port shutdown (Port Security) or alert the SOC (Security Operations Center) instantly.