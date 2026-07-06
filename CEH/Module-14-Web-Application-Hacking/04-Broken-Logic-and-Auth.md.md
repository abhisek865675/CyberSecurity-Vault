# 04: Broken Authentication & Business Logic Flaws 🔐

Many web applications are entirely secure against traditional payload injections but fail fundamentally at verifying *who* a user is and *what* they are allowed to do. These are logical flaws—errors in human design rather than code execution.

---

## 📌 Table of Contents
1. [Broken Authentication Vectors](#1-broken-authentication-vectors)
2. [IDOR (Insecure Direct Object References)](#2-idor-insecure-direct-object-references)
3. [Business Logic Bypasses](#3-business-logic-bypasses)
4. [Defensive Architecture & Countermeasures](#4-defensive-architecture--countermeasures)

---

## 1. Broken Authentication Vectors

Authentication is how an application verifies a user's identity. When this mechanism is weak, attackers can easily hijack user accounts or escalate their privileges.

### Common Authentication Failures:
* **Credential Stuffing:** Attackers take massive lists of breached usernames and passwords from other website hacks and automate login attempts against your application, assuming users reuse their passwords.
* **Brute-Forcing:** Repeatedly guessing passwords against a login portal that lacks rate-limiting or account-lockout mechanisms.
* **Weak Session Management:** * Session IDs that do not expire after a user logs out.
  * Predictable session cookies (e.g., `Cookie: session_id=user123` instead of a long, randomized cryptographic string).
* **Insecure Password Recovery:** Relying on easily researchable "Security Questions" (e.g., "What high school did you attend?") which can be found via a target's LinkedIn or Facebook profile.

---

## 2. IDOR (Insecure Direct Object References)

Also known in modern API testing as **BOLA (Broken Object Level Authorization)**, IDOR is one of the most common and devastating vulnerabilities found in Bug Bounties today.

### The Mechanism
An IDOR occurs when an application provides direct access to objects based on user-supplied input, but **fails to verify if the user requesting the object actually owns it.**



* **The Normal Request:** A user logs in and wants to view their own profile receipt. The browser sends:
  ```http
  GET /api/v1/receipts/download?account_id=8001 HTTP/1.1
  Host: target.com
  Cookie: session=Valid_User_A_Token 
  ```

**The Exploit:** The attacker intercepts this request in Burp Suite and simply changes the sequential number to a different user's ID .

``` http
GET /api/v1/receipts/download?account_id=8002 HTTP/1.1
Host: target.com
Cookie: session=Valid_User_A_Token
```

- **The Impact:** If the backend database only checks if `Valid_User_A_Token` is logged in, but _doesn't_ check if `User A` actually owns `account_id=8002`, the server will leak User B's private receipt.
    

## 3. Business Logic Bypasses

Business logic flaws happen when an attacker manipulates the intended flow of an application to achieve a result the developers never anticipated.

### Real-World Examples:

1. **The Negative Price Exploit (E-commerce):**
    
    - An attacker intercepts the "Add to Cart" request for a laptop costing $1,000.
        
    - They also add a $5 pair of cables to the cart.
        
    - Using Burp Suite, they change the quantity of the cables to `-200`.
        
    - The server calculates: `(1 * $1000) + (-200 * $5) = $1000 - $1000 = $0`.
        
    - The attacker checks out and gets the laptop for free because the developer never enforced a rule stating "Quantity must be greater than zero."
        
2. **Skipping Workflow Steps:**
    
    - An application requires users to pass a payment gateway `/checkout/pay` before reaching `/checkout/download`.
        
    - The attacker forcefully browses directly to `/checkout/download` after logging in, skipping the payment page entirely. If the backend doesn't verify the payment status upon page load, the attacker gets the product for free.
        

## 4. Defensive Architecture & Countermeasures

Because logical flaws do not rely on malicious syntax, they cannot be blocked by automated Web Application Firewalls (WAFs). They must be fixed in the application's core code.

### A. Securing Authentication

- **Implement MFA:** Enforce Multi-Factor Authentication universally.
    
- **Rate Limiting:** Lock accounts or introduce CAPTCHAs after 5 failed login attempts to kill brute-force automation.
    
- **Cryptographic Sessions:** Generate high-entropy session IDs that expire on the server side the moment a user clicks "Logout."
    

### B. Defeating IDOR

- **Drop Sequential IDs:** Never use predictable integers (`user=1`, `user=2`) for database records. Use **UUIDs** (Universally Unique Identifiers) like `user=550e8400-e29b-41d4-a716-446655440000`. An attacker cannot guess a UUID.
    
- **Strict Object-Level Checks:** Every single API endpoint must include a server-side check: `IF (Requesting_User_ID == Object_Owner_ID) THEN Allow()`.
    

### C. Validating Business Logic

- **Never Trust the Client:** Prices, discounts, and item availability must be calculated exclusively on the backend server, never passed through hidden HTML form fields or easily manipulated POST parameters.


---
