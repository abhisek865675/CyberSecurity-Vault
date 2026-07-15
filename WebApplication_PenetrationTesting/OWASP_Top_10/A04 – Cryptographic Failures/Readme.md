# OWASP Top 10 - Cryptographic Failures

## Overview
Formerly known as "Sensitive Data Exposure" (in OWASP 2017), this category focuses on security failures related to cryptography, which often lead to information disclosure. The core issue is rarely the algorithm itself, but rather **how data is handled, stored, or transmitted.**

> **Note:** In the current OWASP 2021/2025 standard, this is categorized under **A02:2021-Cryptographic Failures**.

---

## 1. Core Concepts: Encoding, Hashing, and Encryption
Beginners often confuse these three. A cryptographic failure often occurs when developers use the wrong one for the wrong task.

| Method | Purpose | Reversible? |
| :--- | :--- | :--- |
| **Encoding** | Data Transformation (Format change) | Yes (No key required) |
| **Hashing** | Data Integrity (Fingerprint) | No (One-way) |
| **Encryption** | Data Confidentiality | Yes (Requires a key) |

* **Common Failure:** Developers often mistakenly believe "Base64 Encoding" is "Encryption." Since it has no key, it provides **zero security**.

---

## 2. Common Vulnerabilities (Red Flags)
When auditing an application, look for these common red flags:

* **Weak Algorithms:** Usage of deprecated algorithms like `MD5`, `SHA-1`, `DES`, or `RC4`.
* **Hardcoded Keys:** Storing encryption keys directly in the source code, frontend JavaScript, or mobile APKs.
* **Insecure Transmission:** Sending sensitive data (credentials, PII) over unencrypted `HTTP` instead of `HTTPS`.
* **Lack of Salt/Pepper:** Storing passwords with simple hashes instead of salted, modern hashes (like `Argon2` or `Bcrypt`).
* **Insecure Storage:** Storing sensitive information in local storage, plain-text configuration files, or logs.

---

## 3. Pentesting Methodology
When testing for Cryptographic Failures, follow this workflow:

1.  **Identify Data Flow:** Where is the data going? Is it being sent via POST requests, cookies, or URL parameters?
2.  **Analyze Encoding/Encryption:**
    * If you see a string like `dGVzdA==`, check if it's Base64.
    * If the data appears random/binary, it might be encrypted. Check if you can find a `key` or `IV` (Initialization Vector) in the JavaScript files or source code.
3.  **Verify Transport Security:** Use Burp Suite or browser DevTools to ensure `Secure`, `HttpOnly`, and `SameSite` flags are set on cookies. Check for `Strict-Transport-Security` (HSTS) headers.
4.  **Test for Hash Strength:** If you find a hash, try to crack it using tools like `hashcat` or `John the Ripper` to see if it is salted or uses a weak algorithm.

---

## 4. Remediation (How to Fix)
To prevent cryptographic failures, developers should:

* **Encrypt Data at Rest and in Transit:** Use strong protocols (TLS 1.2+) for transit and robust algorithms (AES-256) for storage.
* **Never Roll Your Own Crypto:** Always use established, peer-reviewed libraries (e.g., Libsodium, OpenSSL).
* **Key Management:** Store keys securely using Hardware Security Modules (HSM) or cloud-native Key Management Services (KMS). Never commit keys to version control.
* **Use Modern Hashing:** Use `Argon2`, `scrypt`, or `bcrypt` for password storage.

---

