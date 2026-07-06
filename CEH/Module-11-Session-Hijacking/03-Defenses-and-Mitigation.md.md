# 03: Session Hijacking Defenses & Mitigation 🛡️

Securing an application against session hijacking requires defending both the transit layer and the browser storage environment. If an attacker cannot read the session token, they cannot hijack the identity.

---

## 📌 Table of Contents
1. [Cookie Security Attributes (The Core Defense)](#1-cookie-security-attributes-the-core-defense)
2. [Session Lifecycle Management](#2-session-lifecycle-management)
3. [Session Binding & Fingerprinting](#3-session-binding--fingerprinting)
4. [Defensive Implementation Matrix](#4-defensive-implementation-matrix)

---

## 1. Cookie Security Attributes (The Core Defense)

When a web application sets a session cookie, it should use explicit browser instructions (attributes) to restrict how that cookie can be accessed or transmitted.



* **HttpOnly:** This flag completely prevents client-side scripts (like JavaScript) from accessing the cookie. Even if an attacker successfully executes a **Cross-Site Scripting (XSS)** attack, `document.cookie` will return empty, completely neutralizing their ability to steal the token via script.
* **Secure:** This flag instructs the browser to *only* transmit the cookie over encrypted (**HTTPS**) connections. This prevents **Side-Jacking** attacks over unencrypted Wi-Fi or cleartext HTTP networks.
* **SameSite:** This flag controls whether cookies are sent with cross-site requests, providing robust protection against Cross-Site Request Forgery (CSRF).
    * `SameSite=Strict`: The cookie is only sent if the request originates from the same site.
    * `SameSite=Lax`: The cookie is withheld on cross-subsite subrequests (like images), but sent when a user navigates to the origin site.

### Secure Cookie Header Example:
```http
Set-Cookie: session_id=xyz123456; Secure; HttpOnly; SameSite=Strict;
```

## 2. Session Lifecycle Management

A token should not remain valid forever. Restricting the lifespan of a session mitigates the window of opportunity for an attacker.

- **Re-Generation Post-Authentication:** To defeat **Session Fixation**, an application must explicitly destroy the anonymous guest session ID and issue a completely new, high-entropy session ID the exact millisecond the user logs in.
    
- **Session Timeouts:** * _Idle Timeout:_ Automatically invalidates the session if the user is inactive for a set period (e.g., 15 minutes).
    
    - _Absolute Timeout:_ Forces a logout after a fixed period (e.g., 24 hours), regardless of user activity, ensuring old intercepted tokens decay.
        
- **Secure Logout Execution:** Clicking "Logout" must clear the cookie from the client browser _and_ explicitly delete the session record from the server-side database/backend cache.
    

## 3. Session Binding & Fingerprinting

If a high-value application needs to ensure a token hasn't changed hands, it can bind the session token to the user's specific context.

- **IP/User-Agent Binding:** The server logs the user's initial incoming IP address and browser `User-Agent` string during login. If a request arrives with a valid session token but from a completely different country or browser type, the server flags it as a hijacking attempt, terminates the session, and prompts for re-authentication.
    
- _The Challenge:_ This can cause false positives if a legitimate user moves from Wi-Fi to a cellular network (changing their IP address).
    

## 4. Defensive Implementation Matrix

| **Vulnerability Vector** | **Root Cause**             | **Technical Mitigating Control**                                |
| ------------------------ | -------------------------- | --------------------------------------------------------------- |
| **XSS Cookie Theft**     | Lack of script isolation   | Apply the `HttpOnly` flag to session cookies.                   |
| **Session Side-Jacking** | Plaintext network transit  | Apply the `Secure` flag; enforce global HTTPS via HSTS.         |
| **Session Fixation**     | Reused anonymous tokens    | Invalidate token and issue a new ID upon authentication.        |
| **Token Prediction**     | Low-entropy/Sequential IDs | Use cryptographically secure pseudo-random generators (CSPRNG). |
