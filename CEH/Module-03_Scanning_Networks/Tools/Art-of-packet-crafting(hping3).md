# Art of Packet Crafting (hping3) 🛠️

Welcome to the comprehensive guide on **hping3**. While Nmap is the king of network scanning, **hping3** is the ultimate scalpel for custom packet crafting, firewall testing, and network analysis in CEH. It allows you to modify every single bit of a TCP/IP packet header.

---

## 📌 Table of Contents
1. [What is hping3?](#-what-is-hping3)
2. [Basic Syntax & Core Modes](#-basic-syntax--core-modes)
3. [Practical Use Cases & Commands](#-practical-use-cases--commands)
4. [Advanced Techniques (Spoofing & Evasion)](#-advanced-techniques-spoofing--evasion)
5. [Cheat Sheet Summary](#-cheat-sheet-summary)

---

## 📖 What is hping3?
`hping3` is a command-line oriented TCP/IP packet assembler/analyzer. It is widely used in ethical hacking for:
* Testing firewall rules and Intrusion Detection Systems (IDS).
* Advanced port scanning (especially when standard tools are blocked).
* Path MTU discovery and advanced traceroute.
* Performance and stress testing (DoS/DDoS analysis).

---

## ⚡ Basic Syntax & Core Modes

The basic structure of an hping3 command is:
> `hping3 [Mode] [Flags] [Target IP]`

### Core Modes (Choose One)
* `-0` or `--rawip` : Raw IP mode
* `-1` or `--icmp`  : ICMP mode (behaves like a customizable ping)
* `-2` or `--udp`   : UDP mode
* *(Default)*       : TCP mode (if no mode is specified, hping3 defaults to TCP)

### Common TCP Flags
* `-S` : Set SYN flag
* `-A` : Set ACK flag
* `-F` : Set FIN flag
* `-R` : Set RST flag
* `-P` : Set PSH flag
* `-X` : Set Xmas flags (F, P, U flags together)

---

## 📂 Practical Use Cases & Commands

### 1. ICMP Ping Sweep / Host Discovery
Standard ping uses ICMP Echo Request. With hping3, you can send a custom ICMP echo request:
```
hping3 -1 192.168.1.1
```

### 2. SYN Scan (Stealth Scanning)

Send a SYN packet to a specific port (e.g., port 80) to check if it's open without completing the 3-way handshake:

Bash

```
hping3 -S -p 80 192.168.1.1
```

- **Open Port Response:** Target replies with `SA` (SYN-ACK).
    
- **Closed Port Response:** Target replies with `RA` (RST-ACK).
    

### 3. UDP Port Scanning

Since UDP is connectionless, detecting open ports is tricky. Send a custom UDP packet to port 53 (DNS):

Bash

```
hping3 -2 -p 53 192.168.1.1
```

- **Closed Port Response:** Target usually replies with an `ICMP Destination Unreachable` packet.
    

### 4. Advanced Traceroute (Bypassing Firewalls)

If a firewall blocks standard ICMP traceroute packets, you can perform a traceroute using TCP SYN packets over port 80:

Bash

```
hping3 -S -p 80 --traceroute 192.168.1.1
```

## 🛡️ Advanced Techniques (Spoofing & Evasion)

### 1. Source IP Spoofing

Hide your real identity or simulate a request coming from a trusted internal IP address:

Bash

```
hping3 -S -p 80 -a 10.0.0.5 192.168.1.1
```

_Note: You won't receive the response back because the target will reply to the spoofed IP (`10.0.0.5`). Use Wireshark on a broader network position to observe._

### 2. Random Source IP (SYN Flood Stress Testing)

Test how well a firewall or target system handles a massive influx of connections from completely randomized source IPs:

Bash

```
hping3 -S -p 80 --flood --rand-source 192.168.1.1
```

_⚠️ **Warning:** Use `--flood` only in local labs or authorized environments. It consumes full bandwidth instantly._

### 3. Data Formatting & Payload Insertion

You can append custom data or file payloads to your packets to test deep packet inspection (DPI):

Bash

```
hping3 -S -p 80 -d 100 -E payload.txt 192.168.1.1
```

- `-d 100`: Defines a data payload size of 100 bytes.
    
- `-E payload.txt`: Injects the contents of `payload.txt` into the packet body


---

⚠️ **Disclaimer:** This document is strictly for educational purposes and official CEH laboratory practice. Unauthorized scanning of networks is punishable under cyber law. Always stick to your sandboxed environments.