# Software and Data Integrity Failures (Supply Chain Attacks)

## 📌 Overview
Software and Data Integrity Failures occur when an application relies upon plugins, libraries, or modules from untrusted sources, CI/CD pipelines, or Content Delivery Networks (CDNs) without verifying their integrity. 

**The Real-World Analogy (The 5-Star Restaurant):**
Imagine a highly secure 5-star restaurant with top-tier hygiene (your secure source code). However, the restaurant buys tomato sauce from a third-party vendor. If a hacker poisons the sauce at the vendor's warehouse, the restaurant's secure kitchen will unknowingly serve poisoned food. This is precisely how **Software Supply Chain Attacks** work—compromising the third-party dependencies to infect the secure main application.

---

## 🛑 Core Vulnerabilities

### 1. Malicious Dependencies & Typosquatting
Modern developers rely heavily on package managers (NPM, PyPI, RubyGems). Hackers upload malicious packages with names highly similar to popular legitimate packages (e.g., `request-componant` instead of `request-component`). If a developer accidentally installs the typo version, malicious code executes directly on the server.

### 2. Dependency Confusion
Internal networks often use private packages. If a hacker discovers the name of a private internal package (e.g., `internal-payment-api`), they can publish a malicious package with the exact same name on a public repository (like npmjs.com) but with a massively inflated version number (e.g., `v99.0.0`). The server's automated build tools may fetch the public, malicious package, thinking it is a required update.

### 3. Poisoned CI/CD Pipelines & Automated Updates
Hackers target the build process itself (Jenkins, GitLab Runners, GitHub Actions) or compromise a legitimate library developer's account to push a backdoored update. When the target server automatically fetches the latest update, the backdoor is deployed without requiring any authentication bypass.

### 4. Insecure Deserialization
When an application takes serialized data (like a user session cookie) from an untrusted source and deserializes it without verifying its integrity (e.g., checking a cryptographic signature), attackers can modify the object to manipulate application logic or achieve Remote Code Execution (RCE).

---

## 💥 Impact
* **Mass Compromise:** A single compromised dependency can infect thousands of organizations simultaneously (e.g., SolarWinds, XZ Utils backdoor).
* **Pre-Auth Remote Code Execution (RCE):** Attackers gain direct server execution privileges without needing to bypass firewalls, authentication, or WAFs.
* **Data Exfiltration:** Malicious packages can silently harvest environment variables, AWS IAM keys, and database credentials during the build process.

---

## 🛡️ Remediation & Defense

1. **Verify Integrity (Checksums/Hashes):** Always use cryptographic signatures or checksums (e.g., SHA-256) to verify that the downloaded package matches the expected original file. Subresource Integrity (SRI) should be used for frontend scripts.
2. **Private Package Managers:** Route all package downloads through a secure, internal, and vetted repository rather than pulling directly from public internet sources.
3. **Lock Dependencies:** Strictly pin package versions in lockfiles (e.g., `package-lock.json`). Do not use wildcards (like `^1.2.0`) that automatically pull new, potentially compromised updates.
4. **Secure the CI/CD Pipeline:** Implement strict access controls on Jenkins/GitLab runners and scan dependencies for known vulnerabilities (using tools like OWASP Dependency-Check or Snyk) before deployment.

---

## 🎯 Practice & Labs

* **Data Integrity (Deserialization):** PortSwigger Web Security Academy (Insecure Deserialization path).
* **Supply Chain & Build Pipelines:** TryHackMe (Dependency Confusion room, Hacking Jenkins pipelines).
* **Environment Enumeration:** Hack The Box (Medium/Hard machines requiring exploitation of outdated third-party internal components).