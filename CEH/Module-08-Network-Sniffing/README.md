# Module 08: Network Sniffing & MITM 📡

Packet sniffing is the practice of intercepting and logging traffic passing over a digital network. As data streams between a source and a destination, it can be captured, analyzed, and sometimes even manipulated. This module covers both **Passive Sniffing** (analyzing traffic) and **Active Sniffing** (Man-in-the-Middle attacks).

---

## 📌 Module Overview
This module is divided into three critical areas:

1. **Foundations:** Understanding how data flows through switches and hubs, and what "Promiscuous Mode" actually does.
2. **Wireshark Mastery:** The industry standard for protocol analysis. If you can use Wireshark, you can troubleshoot almost any network issue.
3. **MITM Attacks:** Using tools like `Ettercap` or `Bettercap` to perform ARP Spoofing and intercept credentials in cleartext.

---

## 🧠 Key Concepts
* **Passive Sniffing:** You are a ghost. You listen to traffic without altering it. This is useful for gathering data (usernames, passwords, sensitive files) without the victim ever knowing.
* **Active Sniffing (MITM):** You trick the network into sending data to *you* before it reaches the intended destination. This involves poisoning ARP tables.
* **Promiscuous Mode:** The state of a network card that allows it to capture all traffic on a network segment, not just traffic addressed to its own MAC address.

---

## 🛠️ Tools of the Trade
* **Wireshark:** The gold standard for packet analysis.
* **TCPDump:** The command-line equivalent for sniffing and saving traffic to `.pcap` files.
* **Ettercap / Bettercap:** Powerful suites for performing Man-in-the-Middle attacks.
* **Arpwatch:** For monitoring ARP activity on a network.

---

⚠️ **Ethical & Legal Disclaimer:**
Sniffing traffic on a network you do not own or have explicit permission to audit is **illegal**. Unauthorized interception of communications (even if unencrypted) is a serious privacy violation and a crime in most jurisdictions. Use these techniques **only** in isolated lab environments.