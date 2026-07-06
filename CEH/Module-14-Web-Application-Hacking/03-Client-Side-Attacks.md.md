# 03: Client-Side Attacks (XSS & CSRF) 🌐

While injection attacks target the backend database or operating system, client-side attacks target the application's users. The attacker leverages vulnerabilities in the web application to execute malicious code inside the victim's browser, steal session tokens, or force unapproved account actions.

---

## 📌 Table of Contents
1. [The Execution Context: Client vs. Server](#1-the-execution-context-client-vs-server)
2. [Cross-Site Scripting (XSS) Breakdown](#2-cross-site-scripting-xss-breakdown)
3. [Cross-Site Request Forgery (CSRF)](#3-cross-site-request-forgery-csrf)
4. [Defensive Countermeasures](#4-defensive-countermeasures)

---

## 1. The Execution Context: Client vs. Server

It is a common misconception that when a hacker runs an XSS attack, they are hacking the server hosting the website. They are not.
* **Server-Side Attack:** Code executes on the host machine (e.g., SQLi queries processing in the database).
* **Client-Side Attack:** The server delivers the webpage normally, but the webpage contains a malicious script payload. The script executes locally inside the victim's web browser framework using their local browser permissions.

---

## 2. Cross-Site Scripting (XSS) Breakdown

XSS occurs when an application includes untrusted data in a web page without proper validation or escaping. This allows an attacker to inject malicious JavaScript into the browser session.



### 🧩 The Three Primary Types of XSS

#### A. Reflected XSS (Non-Persistent)
* **The Mechanism:** The malicious script is part of the request sent to the server (usually inside a URL parameter) and is immediately reflected back in the HTTP response page.
* **The Delivery:** The attacker must trick the victim into clicking a specially crafted malicious link.
* **Example Payload URL:**
  ```text
  [http://target.com/search.php?q=](http://target.com/search.php?q=)<script>alert(document.cookie)</script> 
  ```

#### B. Stored XSS (Persistent)

- **The Mechanism:** The most dangerous type of XSS. The application accepts input containing a script payload and permanently stores it on the server (e.g., in a database, a comment section, an online forum, or a chat profile field).
    
- **The Delivery:** Every single user who visits that specific compromised page will automatically download and execute the payload in their browser.
    

#### C. DOM-Based XSS

- **The Mechanism:** The vulnerability exists entirely on the client-side source code. The payload is executed inside the browser due to the webpage's JavaScript processing input from a source (like the URL hash `location.search`) and passing it dangerously to an execution sink (like `element.innerHTML`) without talking to the server at all.
    

## 3. Cross-Site Request Forgery (CSRF)

CSRF (pronounced _sea-surf_) forces an authenticated victim's web browser to send a forged HTTP request to a vulnerable web application.

### How it Works:

1. **The Target Session:** A user logs into their online banking portal (`bank.com`). The browser stores their authentication session cookie.
    
2. **The Trap:** Without logging out, the user browses to a completely different malicious movie streaming site run by an attacker.
    
3. **The Forgery:** The attacker's site contains a hidden form that auto-submits an HTTP request to the banking app:
    
    HTML
    
    ```
    <img src="[http://bank.com/transfer?amount=10000&to=attacker_account](http://bank.com/transfer?amount=10000&to=attacker_account)" width="0" height="0">
    ```
    
4. **The Automatic Cookie Delivery:** Because browsers automatically attach any active session cookies whenever a request is fired to a domain, `bank.com` receives the valid cookie, thinks the real user intentionally initiated the transfer, and moves the money.
    

## 🛠️ XSS vs. CSRF: Core Comparison

|**Feature**|**Cross-Site Scripting (XSS) 💻**|**Cross-Site Request Forgery (CSRF) 🔄**|
|---|---|---|
|**Primary Goal**|To execute arbitrary code/scripts inside the target's browser.|To force an authorized user into running an action they didn't intend to.|
|**Trust Vector**|Exploits the user's trust in a specific website.|Exploits a website's automatic trust in a user's browser credentials.|
|**Core Impact**|Session hijacking, session token theft, keystroke logging.|Unauthorized account modifications (password changes, purchases, money transfers).|

## 4. Defensive Countermeasures

### A. Defending Against XSS

- **Context-Aware Output Encoding:** Before rendering user input back onto an HTML page, convert syntax-specific characters into safe HTML entities (e.g., converting `<` to `&lt;` and `>` to `&gt;`). The browser will display the symbol but refuse to execute it as a script element.
    
- **HTTPOnly Cookie Flag:** Always configure session cookies with the `HttpOnly` flag. This explicitly blocks any client-side JavaScript from accessing the `document.cookie` string, making token theft via XSS impossible.
    
- **Content Security Policy (CSP):** An HTTP response header that restricts where resources (scripts, styles, images) can be loaded from. A strict CSP blocks inline scripts from firing entirely.
    

### B. Defending Against CSRF

- **Anti-CSRF Tokens (Synchronizer Tokens):** The server generates a unique, unpredictable cryptographic token for the user's current session. When a form is submitted, the application validates the token. Since an external attacker cannot read this unique token from another site, forged requests fail validation.
    
- **SameSite Cookie Attribute:** Configure session cookies with `SameSite=Strict` or `SameSite=Lax`. This instructs the browser to refuse to attach cookies during cross-site third-party link requests, neutralizing the CSRF transmission vector completely.
    

> **💡 Red Team Perspective:**
> 
> Modern frameworks like React and Angular have built-in context encoding that blocks traditional XSS payloads by default. To find an XSS vulnerability on modern apps, red teamers search for instances where developers explicitly bypass these protections for UI flexibility, such as using properties like `dangerouslySetInnerHTML`


---
