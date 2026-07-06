# Session Hijacking Concepts 🔑

To understand how an attacker can steal a session, you must first understand how web applications and network protocols maintain communication. Because the foundation of the internet is inherently detached, developers had to build "state" on top of stateless protocols. This layer is what attackers target.

---

## 📌 Table of Contents
1. [The Problem: Stateless Protocols](#1-the-problem-stateless-protocols)
2. [Network-Level vs. Application-Level Hijacking](#2-network-level-vs-application-level-hijacking)
3. [Spoofing vs. Hijacking (The Timeline)](#3-spoofing-vs-hijacking-the-timeline)
4. [The Three Phases of a Hijacking Attack](#4-the-three-phases-of-a-hijacking-attack)

---

## 1. The Problem: Stateless Protocols

The core protocol of the web, **HTTP**, is completely stateless. It does not naturally remember past requests. 
* To fix this, when you log into a website, the server creates a unique string of characters called a **Session ID** (or session token).
* The server sends this token back to your browser, which stores it as a **Cookie**.
* For every subsequent click (e.g., viewing your profile, adding an item to a cart), your browser automatically attaches that cookie to the request.

If an attacker intercepts or predicts this token, they can present it to the server. The server cannot tell the difference between the legitimate user and the attacker holding the valid token.

---

## 2. Network-Level vs. Application-Level Hijacking

Session hijacking happens at two completely different layers of the OSI model. As a pen tester, you must distinguish between them.



### A. Network-Level Hijacking (Layers 3 & 4)
This targets the underlying transport and network protocols. The attacker intercepts or alters packets directly on the wire.
* **Mechanism:** Most commonly targets **TCP Sessions**. The attacker predicts the next **TCP Sequence Number ($SEQ$)** in an active connection, knocks the legitimate client offline (via a DoS or Reset packet), and injects their own packets into the stream.
* **Difficulty:** High. It requires precision timing and network visibility.

### B. Application-Level Hijacking (Layer 7)
This targets the web application layer. The attacker doesn't care about TCP packets; they want the **HTTP Session Token** or **JWT (JSON Web Token)**.
* **Mechanism:** The attacker steals the session cookie via flaws in the web application (like Cross-Site Scripting) or by sniffing unencrypted Wi-Fi traffic.
* **Difficulty:** Medium to Low. This is the most common form of hijacking on the modern web.

---

## 3. Spoofing vs. Hijacking (The Timeline)

While both techniques involve identity theft on a network, their execution timing is completely different:

| Feature | Session Spoofing | Session Hijacking |
| :--- | :--- | :--- |
| **Timing** | **Pre-Authentication.** Happens *before* a session is established. | **Post-Authentication.** Happens *after* a user successfully logs in. |
| **Method** | Attacker crafts fake credentials or pretends to be a target machine to start a *new* connection. | Attacker intercepts an *existing*, live connection and takes it over mid-stream. |
| **Target** | Bypassing initial authentication controls. | Bypassing active tracking tokens (bypassing MFA entirely). |

---

## 4. The Three Phases of a Hijacking Attack

Regardless of the layer, a successful session hijacking attack almost always follows these three strategic phases:

1. **Tracking/Sniffing:** The attacker finds a target with an active connection to a server. They monitor the traffic flow or look for cross-site vulnerabilities.
2. **Desynchronization:** The attacker interrupts the connection between the legitimate user and the server. This can be done by flooding the user with packets (DoS) or sending a fake `TCP RST` packet to break their connection status.
3. **Injection:** The attacker steps into the gap. They assume the user's identity by sending packets with the correct anticipated sequence numbers or by presenting the stolen session cookie to the web application.

---

> **💡 Key Takeaway for defenders:**
> Application-level hijacking completely neutralizes Multi-Factor Authentication (MFA). Why? Because MFA is checked during the *login phase*. If an attacker steals the session token generated *after* the MFA check, they bypass the login prompt entirely and go straight into the authenticated ecosystem.