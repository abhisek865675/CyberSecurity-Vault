# 02: Wireshark Mastery 🦈

Wireshark is a powerful network protocol analyzer. It allows you to capture live traffic from a network interface and analyze it in real-time or from a saved capture file (`.pcap`).

---
## 📌 Table of Contents
1. [The 3-Pane Interface](#-the-3-pane-interface)
2. [Capture vs. Display Filters](#-capture-vs-display-filters)
3. [Following TCP Streams](#-following-tcp-streams)
4. [Commonly Used Filters](#-commonly-used-filters)
5. [Extracting Sensitive Data](#-extracting-sensitive-data)

---

## 1. The 3-Pane Interface
When you open a capture, Wireshark organizes the data into three main panes:

1. **Packet List Pane:** Shows all packets captured in chronological order. Includes columns for Time, Source IP, Destination IP, Protocol, and Info.
2. **Packet Details Pane:** Expands the selected packet to show its structure (Frame, Ethernet, IP, TCP/UDP, and Application layer data).
3. **Packet Bytes Pane:** Displays the raw hexadecimal and ASCII data of the selected packet.

---

## 2. Capture vs. Display Filters
One of the most common mistakes beginners make is confusing these two filters.



* **Capture Filters (BPF Syntax):** Applied *before* you start capturing. They tell Wireshark: "Only save packets that meet this criteria."
    * *Example:* `host 192.168.1.1` (Only saves traffic to/from this IP).
* **Display Filters (Wireshark Syntax):** Applied *after* you have captured the data. They tell Wireshark: "Hide everything except these packets."
    * *Example:* `ip.addr == 192.168.1.1` (Only shows traffic to/from this IP).

---

## 3. Following TCP Streams
This is the "magic" feature for attackers and defenders. When a conversation spans multiple packets (like a website login or a chat), Wireshark can reassemble them for you.

* **How to use:** Right-click any packet belonging to a TCP session -> **Follow** -> **TCP Stream**.
* **Result:** A new window will appear, showing the full conversation in a clean, readable text format. This is how you see cleartext passwords in protocols like HTTP, FTP, or Telnet.

---

## 4. Commonly Used Filters
Keep these handy for your lab exercises:

| Goal | Filter Syntax |
| :--- | :--- |
| **Filter by IP** | `ip.addr == 192.168.1.5` |
| **Filter by Protocol** | `http` or `dns` or `ftp` |
| **Exclude traffic** | `!(arp)` |
| **Find POST requests** | `http.request.method == "POST"` |
| **Filter by Port** | `tcp.port == 80` |

---

## 5. Extracting Sensitive Data
If a network is using unencrypted protocols, you can extract files directly from the capture:

1. **Go to:** `File` -> `Export Objects` -> `HTTP...`
2. This will list all images, HTML pages, and files transmitted over the captured HTTP traffic.
3. You can click `Save All` to download everything the victim downloaded during your sniffing session.

---

> **💡 Pro Tip for CEH:**
> Always look for `HTTP/POST` requests. These are the most common ways that login forms send usernames and passwords to a server. If you filter for `http.request.method == "POST"` and `Follow TCP Stream`, you can often see the plaintext credentials right there.

---

⚠️ **Ethical Warning:** Use Wireshark only on networks you own. Capturing traffic on public Wi-Fi (like coffee shops) to sniff passwords is a federal crime.