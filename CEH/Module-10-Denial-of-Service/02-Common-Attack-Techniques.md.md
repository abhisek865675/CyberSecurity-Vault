#  Common DoS and DDoS Attack Techniques ⚔️

Attackers do not just randomly flood traffic; they target specific layers of the OSI model and exploit the inner workings of network protocols. This guide breaks down the core mechanics of the most heavily tested attacks in security certifications.

---

## 📌 Table of Contents
1. [The SYN Flood (Layer 4 Protocol Attack)](#1-the-syn-flood-layer-4-protocol-attack)
2. [Slowloris (Layer 7 Application Attack)](#2-slowloris-layer-7-application-attack)
3. [Volumetric Floods: UDP & ICMP](#3-volumetric-floods-udp--icmp)
4. [Legacy & Fragmentation Attacks](#4-legacy--fragmentation-attacks)

---

## 1. The SYN Flood (Layer 4 Protocol Attack)

The SYN flood exploits the standard **TCP Three-Way Handshake** ($SYN \rightarrow SYN-ACK \rightarrow ACK$).

### The Mechanics
1. **The Request:** The attacker sends a massive volume of `SYN` (Synchronize) packets to the target server, using spoofed (fake) source IP addresses.
2. **The Waiting Game:** The server obliges by responding with a `SYN-ACK` packet to each fake IP and allocates a portion of its memory cache (the connection queue or **SYN-Received state**) waiting for the final `ACK`.
3. **Exhaustion:** Because the source IPs are fake, the final `ACK` packets never arrive. The server's memory queue completely fills up with "half-open" connections, forcing it to drop legitimate incoming connection requests.

> **💡 Core Target:** Server memory and connection state tables, *not* network bandwidth.

---

## 2. Slowloris (Layer 7 Application Attack)

Slowloris is a highly efficient "low and slow" attack. It allows a single machine to take down a web server without using significant bandwidth.

<Image src="image_agent_tag_11534567533141652282" alt="Slowloris DDoS attack architecture showing botnet sending partial HTTP requests to target applications" caption="Slowloris attack mechanism" />

### The Mechanics
Instead of overwhelming the web server with thousands of requests per second, Slowloris opens a few hundred connections and holds them open for as long as possible.
1. The attacker sends a partial HTTP request header (e.g., missing the final trailing blank line that signals the end of the header).
2. The web server leaves the connection open, waiting for the rest of the header to arrive.
3. The attacker periodically sends small, dummy header lines (e.g., `X-a: b\r\n`) to keep the connection from timing out.
4. Eventually, the web server exhausts its maximum concurrent connection pool (e.g., `MaxClients` in Apache), rendering it unable to respond to real users.

---

## 3. Volumetric Floods: UDP & ICMP

Unlike the precision of Slowloris, volumetric floods are pure brute force.

### UDP Flood
UDP is a connectionless protocol. The attacker floods random ports on the target with UDP packets. For every packet received, the operating system must check for listening applications. When none is found, it generates an **ICMP Destination Unreachable** packet. This twin cycle of parsing incoming junk and returning error packets quickly burns up line bandwidth and system CPU.

### ICMP (Ping) Flood
The attacker floods the target with `ICMP Echo Request` (ping) packets. The target server tries to respond to every single one with an `ICMP Echo Reply`. If the attacker has more outbound bandwidth than the victim has inbound capacity, the victim's link saturates completely.

---

## 4. Legacy & Fragmentation Attacks

These attacks target flaws in how operating systems assemble data packets.

* **Ping of Death:** The attacker sends an ICMP packet larger than the maximum allowed IP packet size (65,535 bytes). When the target tries to reassemble the fragmented pieces, an internal buffer overflow occurs, crashing the operating system. *(Note: Most modern OS patches have made this obsolete, but it remains a staple concept).*
* **Teardrop Attack:** The attacker sends fragmented packets with overlapping offset fields. When the victim’s operating system attempts to rebuild the original packet, the malformed offsets cause the reassembly logic to crash or reboot the machine.

---

> **⚠️ Analysis Warning:**
> When diagnosing an incident, look at your CPU vs. Bandwidth metrics. A **SYN Flood** will spike your CPU and connection tables while network bandwidth might look completely normal. A **UDP Flood** will max out your network utilization graphs instantly.