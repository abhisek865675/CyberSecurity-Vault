# 🏛️ OWASP A01: - Broken Access Control (BAC)
  
**Category:** Web Application Penetration Testing (WPT)  
**Severity:** High to Critical 🔴  
**Core Principle:** Never trust client-side input. Access control decisions must always be validated strictly on the server side.

---

## 🔍 Fundamental Concepts (First Principles)
Broken Access Control occurs when users can execute actions or access resources outside of their intended permissions. It is critical not to confuse these three distinct mechanisms:
1. **Authentication:** Who is the user? (Verifying identity, e.g., Login).
2. **Session Management:** Maintaining the user's identity via a secure token so they don't have to re-enter credentials on subsequent requests.
3. **Access Control:** The session token is valid, but **"Does this specific user have the authorization to access this resource or function?"**

---

## 👥 Main Attack Vectors & Methodology

### 1. Horizontal Privilege Escalation (IDOR)
* **Logic:** An attacker accesses or modifies resources belonging to another user of the same privilege level.
* **Testing Approach:** Intercept the request in Burp Suite and tamper with object identifiers or user IDs.
  * *Example:* Changing `https://target.com/account?id=505` ➡️ `id=506`

### 2. Vertical Privilege Escalation
* **Logic:** A low-privileged user (e.g., a student) accesses restricted functionalities reserved for high-privileged users (e.g., an Admin).
* **Testing Approach:** Manipulate hidden form fields, parameters, or administrative cookies.
  * *Example:* Changing `isAdmin=false` ➡️ `isAdmin=true`
  * *Forceful Browsing:* Directly navigating to administrative paths (e.g., `/admin` or `/administrator-panel`) often leaked via `/robots.txt`.

### 3. HTTP Method Tampering (PUT / PATCH / DELETE)
* **Logic:** Developers often implement strict access controls on `GET` and `POST` methods but neglect to protect alternative HTTP methods.
* **Methods Cheat-Sheet:**
  * `GET`: Retrieve data (Check for IDOR).
  * `POST`: Create new data.
  * `PATCH`: Partial Update (e.g., Modifying an Admin's password).
  * `PUT`: Complete Overwrite (e.g., Overwriting your profile data to inject `"role": "admin"`).
* **Testing Approach:** Intercept the request, right-click in Burp Suite, select *Change Request Method*, change `GET`/`POST` to `PUT`/`PATCH`, and analyze the server response.

### 4. JWT (JSON Web Token) Exploitation
* **None Algorithm Bypass:** Change the header to `"alg": "none"`, alter the payload data to `"role": "admin"`, remove the signature completely, and send the token.
* **Weak Secret Key:** Extract the signature and use brute-forcing tools like Hashcat or John the Ripper against a wordlist to crack the signing key.

### 5. CORS Misconfiguration
* **Logic:** Occurs when the server sets the header `Access-Control-Allow-Origin: *`. This allows any malicious third-party website to make authenticated requests on behalf of a victim and exfiltrate sensitive data.

---

## 🛠️ Pentesting Checklist (5-Finger Rule)
- [ ] **URL/Directory Fuzzing:** Checked `/robots.txt` and fuzzed for hidden administrative endpoints.
- [ ] **Parameter Pollution / Mass Assignment:** Tried injecting parameters like `"is_admin": true` or `"role": "admin"` inside profile update JSON bodies.
- [ ] **Method Switching:** Toggled request methods from `POST` to `PUT` or `PATCH` to bypass routing restrictions.
- [ ] **CORS Evaluation:** Inspected response headers for dangerous wildcard configurations (`*`) paired with credentials.
- [ ] **Multi-Step Bypass:** Skipped intermediary confirmation steps and directly invoked the final execution endpoint (e.g., `/delete`).

---

## 🤖 Automation Pro-Tip
* **Burp Extension:** `Authorize`
* **Workflow:** Paste the session cookie of a low-privileged user into the Authorize extension. Browse the application normally using an Admin account. The extension automatically replicates every request using the low-privileged user's context and checks if the response length matches. If the length matches, the access control is broken.

---

## 🛡️ Remediation (Developer Guidance)
1. **Centralized Access Control Engine:** Enforce authorization checks via a single, centralized component (e.g., global filters) rather than hardcoding checks across individual pages.
2. **Deny by Default:** Design the system to block access to all newly created endpoints automatically unless explicitly allowed by an access policy.
3. **Principle of Least Privilege:** Run applications using low-privileged service accounts. Never run the web server under `root` or `SYSTEM` privileges to prevent total network compromise via Remote Code Execution (RCE).
4. **Server-Side Validation:** All access control policies must be evaluated strictly on the server side using server-stored session data, never relying on client-side state.

---
## 🔗 Reference Links & Labs
* [PortSwigger BAC Labs](https://portswigger.net/web-security/access-control)
---
