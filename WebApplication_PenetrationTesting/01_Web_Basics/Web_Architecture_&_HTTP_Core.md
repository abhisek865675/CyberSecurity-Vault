# Web Architecture & HTTP Core Fundamentals

This note serves as the technical baseline for Web Application Penetration Testing (WAPT). Understanding how data flows across the web is essential for identifying security flaws.

---

## 🏛️ 1. Web Server vs. Web Application

To effectively target a system, a penetration tester must distinguish between infrastructure flaws and logical flaws.

| Component | Definition | Core Function | Pentester Focus |
| :--- | :--- | :--- | :--- |
| **Web Server** | Hardware/Software infrastructure (e.g., Nginx, Apache, IIS). | Listens for HTTP requests, serves static files (HTML, CSS, images), and routes traffic. | Look for **Security Misconfigurations**, outdated software versions (CVEs), and weak SSL/TLS suites. |
| **Web Application** | Dynamic backend code/logic (written in PHP, Python, NodeJS, Java). | Processes user input, communicates with databases, applies business logic, and generates dynamic responses. | Look for **OWASP Top 10** flaws like SQL Injection, XSS, Broken Access Control, and SSRF. |

> 📌 **First Principles Rule:** > - **Static content** = Handled by the Web Server.
> - **Dynamic/User-driven content** (Search boxes, comment forms, login panels) = Handled by the Web Application and Backend Databases.

---

## 📞 2. The HTTP Protocol (Request / Response Lifecycle)

HTTP (Hypertext Transfer Protocol) is a **stateless**, application-layer protocol used to transfer data over the web.



### 📨 2.1 HTTP Request Structure
When intercepted via Burp Suite, a raw HTTP Request looks like this:

``` http
POST /login HTTP/1.1
Host: target.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Cookie: session_id=xyz123456789
Content-Type: application/x-www-form-urlencoded

username=admin&password=secret123
```

- **Request Line (Line 1):** Contains the **Method** (`GET`, `POST`, `PUT`, `DELETE`), the **Target Path/Endpoint** (`/login`), and the **HTTP Version** (`HTTP/1.1`).
    
    - `GET`: Requests data. Parameters are appended directly to the URL (visible in logs).
        
    - `POST`: Submits data to the server inside the request body (hidden from URL).
        
- **Headers (Metadata):**
    
    - `Host`: Target domain name.
        
    - `User-Agent`: Identifies the client browser and operating system.
        
    - `Cookie`: Transmits session tokens. **Crucial target for Session Hijacking.**
        
- **Request Body:** The actual data payload sent to the server (only present in methods like `POST` or `PUT`).
    

### 📩 2.2 HTTP Response Structure

The server’s answer back to the client:



``` http
HTTP/1.1 200 OK
Server: Nginx/1.18.0
Content-Type: text/html
Set-Cookie: session_id=xyz123456789; Secure; HttpOnly

<html><body><h1>Dashboard</h1></body></html>
```

- **Status Line (Line 1):** Protocol version followed by the **HTTP Status Code** (`200 OK`).
    
- **Response Headers:**
    
    - `Server`: Exposes backend server details. Useful for version-specific exploit research.
        
    - `Set-Cookie`: Forces the browser to store a session token. Watch out for security flags like `Secure` and `HttpOnly`.
        
- **Response Body:** The rendered HTML/CSS page or JSON data returned to the user.
    

### 🔢 2.3 HTTP Status Codes Cheat Sheet

- **`2xx` (Success):** `200 OK` (Request succeeded).
    
- **`3xx` (Redirection):** `302 Found` (Often used to redirect users to a dashboard after a successful login).
    
- **`4xx` (Client Error):** * `403 Forbidden` (Access denied due to authorization controls).
    
    - `404 Not Found` (Target resource does not exist).
        
- **`5xx` (Server Error):** `500 Internal Server Error`. **Critical for Pentesters**; indicates the backend application code or database query crashed due to unhandled input (potential Injection flaw).
    

## 🗺️ 3. URL Anatomy

Every component of a URL tells a pentester where input parameters can be manipulated:

`https://target.com/shop/search?product=laptop&sort=low`

- `https://` -> **Protocol / Scheme**
    
- `target.com` -> **Domain / Host**
    
- `/shop/search` -> **Path / Endpoint** (The application logic processing the request)
    
- `?product=laptop&sort=low` -> **Query Parameters** * `product` and `sort` are parameter keys.
    
    - `laptop` and `low` are user-supplied values. **This is your primary injection testing ground.**
        

## 🛡️ 4. Same-Origin Policy (SOP)

- **Definition:** A critical browser security mechanism that restricts a script loaded from one origin from selecting or interacting with a resource from another origin.
    
- **An Origin consists of:** **Protocol + Domain + Port**.
    
    - `https://example.com:443` is a different origin compared to `http://example.com:443` (Different protocol).
        
    - `https://example.com` is a different origin compared to `https://attacker.com` (Different domain).
        
- **Significance:** SOP prevents a malicious site (`attacker.com`) open in Tab B from executing scripts to steal sensitive session cookies or read data from your bank account (`bank.com`) open in Tab A.
    

## 🔏 5. Data Encodings (Handling and Formatting Data)

Encoding is used to format data for safe transit across different systems. It provides **zero security/secrecy** and can be instantly reversed.

### 5.1 URL Encoding (Percent Encoding)

Converts unsafe/reserved ASCII characters into a `%` followed by their two-digit hexadecimal representation. Prevents breakage of URL structures.

- _Space_ becomes `%20` or `+`
    
- _Single Quote (`'`)_ becomes `%27`
    
- _Payload:_ `admin' OR 1=1--` -> _Encoded:_ `admin%27%20OR%201%3D1--`
    

### 5.2 HTML Encoding

Converts special characters into HTML entities to ensure the browser displays them as raw text instead of executing them as live code.

- `<` becomes `&lt;`
    
- `>` becomes `&gt;`
    
- _Significance:_ If a web app reflects your input safely using HTML encoding, **Cross-Site Scripting (XSS)** is mitigated.
    

### 5.3 Base64 Encoding

Encodes binary data into a clean set of 64 printable ASCII characters (`A-Z`, `a-z`, `0-9`, `+`, `/`). Easily recognized by its Alphanumeric format and trailing padding character `=` or `==`.

- _String:_ `admin` -> _Base64:_ `YWRtaW4=`
    
- _Warning:_ Developers often mistake Base64 for encryption. **Always intercept cookies, decode Base64 strings, modify values (e.g., change `user` to `admin`), re-encode, and replay to test for privilege escalation.**