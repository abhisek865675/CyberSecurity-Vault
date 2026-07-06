# Module 12: Security Infrastructure (Firewalls, IDS, EDR & Architecture) 🏰

To successfully hack a network, you must first understand how it defends itself. This module moves from legacy perimeter defenses to modern, AI-driven endpoint tracking, and concludes with how these devices are physically and logically placed within an enterprise network.

---

## 📌 Module Overview
This module explores the blue team's arsenal. We will break down how traffic is filtered, how malicious behavior is detected, and how attackers are tricked into revealing their techniques.

1. **The Perimeter:** Firewalls (Packet filtering vs. Next-Gen) and Intrusion Detection/Prevention Systems (IDS/IPS).
2. **The Endpoint:** Moving beyond legacy antivirus into EDR (Endpoint Detection and Response) and XDR (Extended Detection and Response).
3. **Deception & Architecture:** Using Honeypots to trap attackers, and mapping out exactly where every security appliance sits in a standard corporate network.

---

## 🧠 The Evolution of Defense
Historically, cybersecurity relied on a "castle-and-moat" methodology. You built a massive, hard outer shell (the Firewall) and trusted everything inside. 

* **The Old Way (Perimeter-focused):** "If you pass the firewall, you are trusted." Attackers learned to bypass the firewall using phishing or compromised credentials, giving them free rein inside the network.
* **The New Way (Assume Breach):** "The perimeter is already compromised." Security now focuses on **Visibility**. EDR and XDR are deployed to monitor every single process, registry change, and memory execution on every laptop and server, hunting for anomalies *inside* the castle walls.

---

## 🛠️ The Security Stack at a Glance
* **Firewall (FW):** The bouncer at the door checking IDs.
* **Intrusion Detection System (IDS):** The security camera recording suspicious behavior and sending alerts.
* **Intrusion Prevention System (IPS):** The security guard who actively tackles someone doing something malicious.
* **EDR/XDR:** The undercover detective tracking an attacker's exact movements across different departments.
* **Honeypot:** The fake vault left open to distract the bank robbers while the police arrive.

---

⚠️ **Red Team Note:** Understanding this infrastructure is critical for evasion. If you don't know the difference between an IDS alert and an EDR behavioral trigger, your reverse shells will be caught immediately during a penetration test.