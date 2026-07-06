# 02: Session Hijacking Attack Vectors & Tools 🎯

To successfully hijack a session, an attacker must acquire the session identifier through exploitation, prediction, or passive interception. This section focuses on the primary application-layer methodologies tested across security frameworks.

<Image src="image_agent_tag_5125596282488690984" alt="Diagram showing the session hijacking workflow: victim signs in, session token is returned, attacker steals the token, and then impersonates the victim to the server" caption="Session Hijacking workflow loop" />

---

## 📌 Table of Contents
1. [Primary Attack Vectors](#1-primary-attack-vectors)
2. [Session Fixation vs. Session Hijacking](#2-session-fixation-vs-session-hijacking)
3. [Network Interception (Side-Jacking)](#3-network-interception-side-jacking)
4. [Tools of the Trade](#4-tools-of-the-trade)

---

## 1. Primary Attack Vectors

### A. Cross-Site Scripting (XSS)
This is the most common path to application-layer session theft. If a web application contains a Stored or Reflected XSS vulnerability, an attacker can inject a malicious script into the page. When the victim's browser executes that script, it accesses the browser's storage mechanisms.
* **The Target:** The script accesses `document.cookie` or local storage and automatically exfiltrates the active session token back to an attacker-controlled listener.

### B. Session Prediction
If an application generates session IDs using a weak, non-random algorithm (e.g., sequentially incrementing IDs, or using predictable patterns like timestamps mixed with basic hashing), an attacker can guess valid active tokens.
* **The Target:** The attacker systematically crafts variations of a known pattern until they stumble upon an active session string.

---

## 2. Session Fixation vs. Session Hijacking

While standard hijacking involves *stealing* a session token after creation, **Session Fixation** involves an attacker *forcing* a specific token onto the victim before login.

[Attacker gets trap token] ---> [Tricks victim into using it] ---> [Victim logs in] ---> [Attacker uses same token to enter]

### The Mechanism

1. The attacker connects to the target website and receives a valid, unauthenticated session token. 
2. The attacker tricks the victim into clicking a link containing that specific fixed session ID (e.g., `http://example.com/?PHPSESSID=MALICIOUS_TOKEN`). 
3. The victim clicks the link and authenticates into their account.
4. **The Flaw:** If the web application fails to issue a *new* session ID upon successful authentication, the old token remains active. The attacker now uses the exact same `MALICIOUS_TOKEN` to access the victim's authenticated profile.
---

## 3. Network Interception (Side-Jacking)

Session Side-Jacking relies on unencrypted data transit paths. 
* **The Setup:** The initial login process might happen over an encrypted channel (HTTPS), but the rest of the application's pages fall back to unencrypted HTTP.
* **The Interception:** An attacker occupying the same local area network (such as a shared Wi-Fi access point) runs an active network sniffer. As the victim navigates the HTTP elements of the site, the browser transmits the session cookie in plain text across the airwaves, allowing the attacker to grab it passively. 

---
## 4. Tools of the Trade

Rather than building complex multi-layer programs from scratch, standard infrastructure testing relies on intercepting proxies and local storage management utilities:

| Tool | Focus Layer | Common Use Case |
| :--- | :--- | :--- |
| **Burp Suite / OWASP ZAP** | Application (Layer 7) | Acts as an inline proxy to intercept HTTP requests, view cookie structures, and replay sessions. |
| **Wireshark** | Transport / Network (Layers 3 & 4) | Used in Side-Jacking scenarios to capture plaintext network cookies sent over unencrypted HTTP pathways. |
| **Cookie Editors** | Browser-level (Client-side) | Browser extensions used to quickly insert, delete, or modify specific key-value pairs inside local browser storage to test application state changes. |

> **💡 Professional Insight:** 
> Session fixation vulnerabilities are frequently tied to poor session lifecycle handling. An application must always invalidate the old anonymous guest session ID the exact millisecond a user clicks "Log In" and replace it with a newly generated, cryptographically secure identifier.