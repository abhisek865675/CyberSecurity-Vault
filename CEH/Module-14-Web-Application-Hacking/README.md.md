# Module 14: Web Application Hacking & OWASP Top 10 🕷️

While firewalls and server hardening protect the infrastructure, the web application itself is often the weakest link. Applications are custom-built by developers, and humans make logical mistakes. Web Application Hacking focuses on manipulating the application's expected logic to access unauthorized data, execute code, or bypass authentication.

---

## 📌 Module Overview
This module dives deep into the most critical vulnerabilities found in modern web applications, heavily guided by the industry-standard OWASP Top 10.

1. **The Tooling:** Mastering intercepting proxies (Burp Suite) to manipulate traffic in real-time.
2. **Server-Side Flaws:** Injection attacks (SQLi) and Broken Authentication.
3. **Client-Side Flaws:** Cross-Site Scripting (XSS) and Cross-Site Request Forgery (CSRF).
4. **Logical Flaws:** Insecure Direct Object References (IDOR/BOLA) and business logic bypasses.

---

## 🧠 The OWASP Top 10 Standard
The **Open Worldwide Application Security Project (OWASP)** is a nonprofit foundation that works to improve the security of software. Every few years, they release the "Top 10" list—a standard awareness document for developers and web application security.

If you are doing a web penetration test or a Bug Bounty, these are the exact categories you are hunting for:
* **Broken Access Control:** Users accessing data they shouldn't (e.g., viewing someone else's cart).
* **Cryptographic Failures:** Leaking sensitive data due to bad encryption.
* **Injection:** Tricking the backend database into executing malicious commands (SQLi).
* **Insecure Design:** Flaws in the core business logic.
* **Security Misconfiguration:** Leaving default accounts active or exposing error messages.

---

## 🛠️ The Primary Weapon: The Intercepting Proxy
You cannot hack web applications effectively using just a browser. A browser automatically formats and sends your requests, hiding the raw HTTP traffic.

**Enter Burp Suite (or OWASP ZAP):**
An intercepting proxy sits exactly between your web browser and the target web server. 
* **The Workflow:** Your browser sends the request $\rightarrow$ The Proxy catches it and pauses it $\rightarrow$ You manually alter the hidden parameters, cookies, or headers $\rightarrow$ You forward the tampered request to the server.


---

⚠️ **Ethical & Legal Disclaimer:**
Web application testing should only be performed on applications where you have explicit permission (e.g., a recognized Bug Bounty program like HackerOne/Bugcrowd, or a local vulnerable lab like OWASP Juice Shop or DVWA). Do not tamper with parameters on live production sites without authorization.