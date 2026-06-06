# Web Application Penetration Testing (WAPT) Methodology

A systematic, industry-standard checklist based on OWASP and OSSTMM frameworks to ensure complete coverage during a web assessment.

---

## 🛰️ Phase 1: Information Gathering & Reconnaissance
*Objective: Map out the target infrastructure, hidden assets, and technologies without necessarily exploiting them.*

### 1.1 Passive Reconnaissance
- [ ] **Whois Lookup & ASN Mapping:** Identify domain ownership and IP ranges.
- [ ] **DNS Enumeration:** Discover DNS records (MX, TXT, NS, A) using tools like `dnsrecon` or `dig`.
- [ ] **Google Dorking (GHDB):** Search for exposed log files, backup files, configuration leaks, or login portals.
  - *Example:* `site:target.com filetype:sql` or `site:target.com inurl:admin`
- [ ] **OSINT Asset Discovery:** Check public code repositories (GitHub, GitLab) for leaked API keys, hardcoded credentials, or source code.

### 1.2 Active Reconnaissance
- [ ] **Subdomain Enumeration:** Find subdomains to expand the attack surface.
  - *Tools:* `subfinder`, `amass`, `assetfinder`
- [ ] **Port Scanning & Service Detection:** Identify open ports and running services on the target IP.
  - *Tools:* `nmap -sV -sC -T4 [target_ip]`
- [ ] **Directory & File Brute-Forcing:** Scan for hidden paths, administrative panels, backup files, or sensitive directories.
  - *Tools:* `gobuster dir`, `ffuf`, `dirb` ,`dirbuster` , `feroxbuster`
  - *Common Extensions:* `.bak`, `.conf`, `.old`, `.git`, `.env`

### 1.3 Tech Stack Identification
- [ ] **Fingerprinting:** Identify the Operating System, Web Server, CMS, and Backend Language.
  - *Tools:* Wappalyzer extension, WhatWeb, or inspecting HTTP Response Headers (`Server:`, `X-Powered-By:`).

---

## 🗺️ Phase 2: Vulnerability Assessment & Mapping
*Objective: Explore every feature of the live web application to locate potential entry points and construct an attack surface map.*

### 2.1 Input & Parameter Mapping
- [ ] **Identify Input Vectors:** Locate all fields accepting user data (Search bars, Contact forms, File upload fields).
- [ ] **Map URL Parameters:** Document query parameters (`?id=`, `?category=`) and hidden form inputs.
- [ ] **Analyze Client-Side Code:** Inspect JavaScript files for hidden API endpoints, hardcoded credentials, or vulnerable libraries.

### 2.2 Privilege & Role Mapping
- [ ] **Identify Roles:** Determine the different access levels available (e.g., Guest, Authenticated User, Moderator, Admin).
- [ ] **Analyze Session Mechanisms:** Inspect how sessions are managed (Cookies, JWT tokens, LocalStorage).

### 2.3 Automated Scanning
- [ ] **Run Burp Suite Scanner:** Execute a passive/active scan on critical entry points to automatically detect low-hanging bugs (Insecure Headers, Outdated Components).

---

## ⚡ Phase 3: Vulnerability Exploitation (OWASP Top 10)
*Objective: Actively test the mapped entry points to confirm and exploit vulnerabilities.*

### 3.1 A01: Broken Access Control
- [ ] Test for **IDOR** by changing parameter IDs (`/user/1001` -> `/user/1002`).
- [ ] Test for **Vertical Escalation** by accessing `/admin` panels from a standard user session.

### 3.2 A03: Injection
- [ ] Test input fields and parameters for **SQL Injection** using single quotes (`'`), payloads, or `sqlmap`.
- [ ] Test for **XSS** by injecting `<script>` tags or HTML entities into reflective and persistent inputs.
- [ ] Test form vectors for **OS Command Injection** using shell command delimiters (`;`, `&&`, `|`).

### 3.3 A07: Identification and Authentication Failures
- [ ] Check for brute-force vectors on login forms (absence of rate-limiting).
- [ ] Inspect session tokens for predictability or weak encoding (Base64 vs Encryption).

### 3.4 A10: Server-Side Request Forgery (SSRF)
- [ ] Force the web server to make requests to internal resources or cloud metadata endpoints (`http://169.254.169.254/`).

---

## 📝 Phase 4: Reporting & Remediation
*Objective: Document discovered flaws professionally and provide actionable fixes for developers.*

- [ ] **Vulnerability Summary:** Name, OWASP category, CVE (if applicable), and CVSS score (Severity).
- [ ] **Proof of Concept (PoC):** Step-by-step instructions to reproduce the vulnerability, including raw Burp Suite Requests/Responses.
- [ ] **Impact Assessment:** Explain what a malicious actor could achieve by exploiting this flaw.
- [ ] **Remediation / Mitigation:** Write secure code snippets or structural server changes required to fix the vulnerability permanently.