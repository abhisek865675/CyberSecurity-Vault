#  Common Social Engineering Attack Vectors 🎣

Attack vectors are the pathways an attacker uses to reach the target. While the psychology (Module 01) remains the same, the delivery method changes based on the attacker's resources and the target's environment.

---

## 📌 Table of Contents
1. [Digital/Remote Vectors](#-digitalremote-vectors)
2. [Physical/In-Person Vectors](#-physicalin-person-vectors)
3. [The "Watering Hole" & Baiting](#-the-watering-hole--baiting)
4. [Vector Comparison Table](#-vector-comparison-table)

---

## 1. Digital/Remote Vectors
These are the most common attacks due to their low cost and high scalability.

* **Phishing:** The "shotgun" approach. Sending mass emails to thousands of people hoping a small percentage will click.
* **Spear Phishing:** Highly targeted. The attacker researches the specific victim (using LinkedIn or public data) to craft a message that seems legitimate (e.g., "Hi [Name], here is the invoice for the project we discussed").
* **Whaling:** Spear phishing directed specifically at high-profile targets like CEOs, CFOs, or other executives.
* **Vishing (Voice Phishing):** Using phone calls (often with spoofed caller IDs) to trick users into revealing credentials or transferring money.
* **Smishing (SMS Phishing):** Phishing attacks delivered via text messages or messaging apps (e.g., "Your package delivery failed, click here to reschedule").



---

## 2. Physical/In-Person Vectors
These require more risk from the attacker but often yield much higher rewards.

* **Tailgating (Piggybacking):** Following an authorized person through a secure door. The attacker relies on the target's desire to be "polite" by holding the door open.
* **Shoulder Surfing:** Observing a target's screen or keyboard from behind to steal passwords, PINs, or sensitive information.
* **Dumpster Diving:** Searching through physical trash to find discarded documents, passwords, or company hardware (like USB drives).



---

## 3. The "Watering Hole" & Baiting
These techniques rely on the target interacting with compromised environments or objects.

* **Watering Hole Attack:** The attacker compromises a website that the target *frequently visits* (e.g., a local news site, a supply chain portal) and infects it with malware. The target is infected simply by visiting the site.
* **Baiting:** The attacker leaves a "bait" item (like a USB stick labeled "Salary Info") in a public place. A curious employee picks it up, plugs it into a company computer, and triggers an automatic payload.
* **Quid Pro Quo:** "Something for something." The attacker promises a benefit (e.g., "I can fix your computer performance") in exchange for information or access.

---

## 4. Vector Comparison Table

| Attack Vector | Effort Level | Target Scope | Primary Goal |
| :--- | :--- | :--- | :--- |
| **Phishing** | Low | Broad (Mass) | Credential Theft |
| **Spear Phishing**| Medium | Narrow (Individual)| Data Exfiltration |
| **Vishing** | High | Individual | Real-time Access |
| **Tailgating** | High | Individual | Physical Access |
| **Baiting** | Low | Opportunistic | System Compromise |

---

> **💡 Pro Tip for Security Professionals:**
> Notice that the **human element** is the common denominator in every single one of these vectors. Whether it's the person who holds the door open or the person who clicks the "Urgent" link, the vulnerability is the victim's social conditioning.

---

⚠️ **Ethical Disclaimer:** These methods are effectively used in "Red Teaming" exercises to test an organization's physical and digital security. Never use these techniques to bypass security controls on systems or networks you are not authorized to test.