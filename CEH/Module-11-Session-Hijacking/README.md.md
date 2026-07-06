# Module 11: Session Hijacking 🍪

Session Hijacking (sometimes called cookie hijacking) is the exploitation of a valid computer session to gain unauthorized access to information or services in a computer system. Instead of guessing a user's password, the attacker simply steals the "session token" generated *after* a successful login.

---

## 📌 Module Overview
This module covers how web applications track users and how attackers exploit flaws in that tracking mechanism.

1. **Foundations:** How HTTP (a stateless protocol) uses cookies and tokens to remember who you are.
2. **Attack Techniques:** Stealing session tokens via Cross-Site Scripting (XSS), Man-in-the-Middle (MITM) sniffing, or Session Fixation.
3. **Mitigation:** Securing cookies using flags like `HttpOnly`, `Secure`, and implementing proper session timeouts.

---

## 🧠 The Core Concept: Spoofing vs. Hijacking
It is common to confuse these two terms, but they happen at different stages of an attack:

* **Spoofing (Pre-Authentication):** The attacker pretends to be someone else *before* authentication happens (e.g., forging a MAC address or IP address to start a connection).
* **Hijacking (Post-Authentication):** The attacker waits for a legitimate user to log in successfully. Once the server issues a valid session ID, the attacker steals that ID and takes over the live, active session.



---

## 🏗️ Why Sessions Exist
The internet runs on **HTTP**, which is inherently **stateless**. This means every time you click a link or refresh a page, the web server treats you like a complete stranger. 
To fix this, web apps use a **Session ID** (stored in a cookie or browser storage). If an attacker gets their hands on your unique Session ID string, they can inject it into their own browser and instantly become *you* in the eyes of the server—no password or MFA required.

---

## 🛠️ Common Tools Used
* **Burp Suite / OWASP ZAP:** To intercept web traffic and modify session tokens on the fly.
* **Cookie Editors (Browser Extensions):** Simple extensions used to manually view, edit, and inject session cookies into a browser.
* **Wireshark:** To sniff unencrypted HTTP traffic and pull session IDs straight out of the cleartext packets.

---

⚠️ **Ethical & Legal Disclaimer:**
Stealing active user sessions on live websites is a severe breach of privacy and a criminal offense. Only practice session hijacking techniques on intentionally vulnerable labs (like OWASP Juice Shop or DVWA) inside your local virtual environment.