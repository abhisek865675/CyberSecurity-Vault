#  Common Mobile Vulnerabilities 🛡️

Mobile apps are not just code; they are ecosystems that interact with local storage, other apps, and remote APIs. Vulnerabilities usually arise when developers "trust" the local environment too much or implement security controls incorrectly.

---

## 📌 Table of Contents
1. [Insecure Data Storage](#1-insecure-data-storage)
2. [Insecure Communication](#2-insecure-communication)
3. [Intent Hijacking (IPC)](#3-intent-hijacking-ipc)
4. [Broken Authentication & Session Management](#4-broken-authentication--session-management)

---

## 1. Insecure Data Storage
Developers often store sensitive data (tokens, PII, passwords) in insecure locations. Because mobile devices can be stolen, local storage is considered a "hostile" environment.



* **Shared Preferences:** Developers often store tokens in cleartext XML files. These are easily readable if the device is rooted.
* **SQLite Databases:** Often left unencrypted. An attacker can simply pull the `.db` file from the app's directory and open it in a viewer.
* **External Storage (SD Card):** Anything stored here is globally readable by *any* app on the phone. Never store sensitive data in `/sdcard/`.

---

## 2. Insecure Communication
Even if the app is secure, the data in transit can be intercepted if the implementation is weak.

* **SSL Pinning Bypass:** As discussed in Dynamic Analysis, apps should pin their server certificates. If they don't, an attacker can perform a Man-in-the-Middle (MITM) attack using Burp Suite and see all traffic in plain text.
* **Cleartext Traffic:** Some apps explicitly disable HTTPS (using `android:usesCleartextTraffic="true"` in the Manifest). This allows attackers to sniff sensitive API calls over public Wi-Fi.

---

## 3. Intent Hijacking (IPC)
Android apps communicate using **Intents** (messages between components). If an app exposes an "exported" component (like an Activity or Service) without proper permissions, other malicious apps on the same device can trigger it.



* **The Attack:** A malicious app sends a crafted Intent to a vulnerable app to trigger private screens, steal data via Content Providers, or perform actions as the victim (e.g., triggering a background transfer).
* **Fix:** Always set `android:exported="false"` in the Manifest for components that are not intended to be accessed by third-party apps.

---

## 4. Broken Authentication & Session Management
Because mobile apps don't have cookies in the traditional web sense, developers often build custom token-based auth systems.

* **Hardcoded Keys:** Storing JWT (JSON Web Token) secrets or encryption keys inside the code.
* **Long-lived Tokens:** Using tokens that never expire. If an attacker extracts this token from the device's storage, they can impersonate the user indefinitely.
* **Improper Biometric Handling:** Some apps use biometrics just to unlock a *local* flag. If you bypass that flag (via Frida), the app unlocks without ever validating the user's actual fingerprint.

---

## 🛠️ Security Checklist for Mobile
If you are auditing a mobile app, always check these four areas:
1. **[ ] Manifest Audit:** Are components exported unnecessarily? Is it debuggable?
2. **[ ] Storage Audit:** Check `/data/data/[package]/` for any files containing credentials or sensitive configs.
3. **[ ] Traffic Audit:** Fire up Burp Suite. If you can read the API traffic, the app is leaking sensitive info.
4. **[ ] Binary Audit:** Use `strings` or `JADX` to search for hardcoded secrets.

---

> **💡 Professional Analysis:**
> The most critical mobile vulnerability is often the **combination** of two small flaws. For example, an app might be secure, but it stores a user's session token in an unencrypted file (`Insecure Storage`). A malicious app on the same phone, if it has "Read External Storage" permissions, can steal that file. Always think about the *entire* ecosystem of the phone, not just the app in isolation.


---
