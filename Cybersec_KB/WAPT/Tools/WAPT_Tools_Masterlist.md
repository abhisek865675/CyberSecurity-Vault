# Web Application Penetration Testing (WAPT) Tools Masterlist

A categorized index of tools utilized across different phases of a web application assessment, complete with standard command syntaxes.

---

## 🛠️ 1. Interception Proxies & Traffic Analysis
*Objective: Intercept, inspect, and modify HTTP/S traffic between the client browser and the server.*

### 1.1 Burp Suite Professional
- **Purpose:** Core platform for web security testing, automation, and logical flaw analysis.
- **Key Modules to Master:**
  - `Proxy`: Intercepting and modifying live requests/responses.
  - `Repeater`: Replaying individual requests with manual payloads.
  - `Intruder`: Automated brute-forcing, fuzzing, and parameter stuffing.
  - `Decoder`: Quick encoding/decoding of URL, Base64, and Hex.
- **Essential Extensions (BApp Store):**
  - `Param Miner`: Finds hidden unlinked parameters.
  - `Autorize`: Automates authorization testing (excellent for A01 Broken Access Control).
  - `Logger++`: Enhanced logging of all background traffic.

---

## 🛰️ 2. Reconnaissance & Asset Discovery
*Objective: Expand the attack surface by identifying domains, live hosts, and open services.*

### 2.1 Subdomain Enumeration
*   **Subfinder:** Fast passive subdomain discovery tool.


```
subfinder -d target.com -o subdomains.txt
```


*   **Amass:** In-depth network mapping and active/passive asset discovery.

 ```
 amass enum -d target.com
 ```
   

### 2.2 Port Scanning & Fingerprinting
*   **Nmap:** Network exploration tool and service version scanner.

    ```
    nmap -sV -sC -T4 -p- -oN nmap_scan.txt [target_ip]
    ```

*   **WhatWeb / Wappalyzer:** Identifies backend technologies, CMS versions, and web servers.

---

## 📂 3. Directory & File Enumeration (Fuzzing)
*Objective: Locate hidden directories, unlinked files, backups, and administrative endpoints.*

*   **Gobuster:** High-speed directory and file brute-forcing written in Go.

    ```
    gobuster dir -u [https://target.com](https://target.com) -w /usr/share/wordlists/dirb/common.txt -x php,html,bak,txt
    ```
    
*   **FFuf (Fuzz Faster U Fool):** Highly customizable web fuzzer used for directories, parameters, and virtual hosts.

    ```
    ffuf -u [https://target.com/FUZZ](https://target.com/FUZZ) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
    ```
    
*   **Dirsearch:** Advanced command-line tool designed for brute-forcing directories.

---

## ⚡ 4. Vulnerability Exploitation Tools
*Objective: Automated detection and exploitation frameworks for specific vulnerability classes.*

### 4.1 SQL Injection (A03)
*   **SQLMap:** Automatic SQL injection and database takeover tool.

    ```
    sqlmap -u "[https://target.com/item.php?id=1](https://target.com/item.php?id=1)" --batch --dbs
    ```
   
    *(For authenticated sessions, pass the Burp request file):*
    
    ```
    sqlmap -r request.txt --batch --level=3 --risk=2
    ```
    

### 4.2 Web Vulnerability Scanners (Automation)
*   **Nikto:** Open-source web server scanner that performs comprehensive tests against dangerous items and outdated software.
  
    ```
    nikto -h [https://target.com](https://target.com)
    ```

# Burp Suite Pro Mastery & Shortcuts

The ultimate configuration and shortcut guide for web traffic interception and manipulation.

---

## ⚡ 1. The Core Pentester Workflow

[ Browser ] ---> ( Burp Proxy: Intercept ON ) ---> [ Repeater / Intruder ] ---> [ Target Server ]

1. **Proxy Tab:** Set `Intercept to ON` to catch requests on the fly. Use `Forward` to send them or `Drop` to kill them.
2. **HTTP History:** The most important tab under Proxy. It logs every single request. Filter by "Show only in-scope items" to clear the noise.
3. **Repeater (Ctrl + R):** Your main sandbox. Modify headers, parameters, or inject payloads here and click "Send" to see the immediate server response.
4. **Intruder (Ctrl + I):** Used for automation (brute-forcing passwords, fuzzing parameters for SQLi/XSS, or enumerating usernames).

---

## ⌨️ 2. Speed Shortcuts (Must-Remember)

Mastering these shortcuts separates a beginner from a professional trainer:

| Shortcut | Action | Context / Usage |
| :--- | :--- | :--- |
| **`Ctrl + R`** | Send to Repeater | Run this from HTTP History or Proxy to test a request manually. |
| **`Ctrl + I`** | Send to Intruder | Run this to send a login request for a brute-force attack. |
| **`Ctrl + U`** | URL Encode selection | Encodes special characters (e.g., space to `%20`, `'` to `%27`). |
| **`Ctrl + Shift + U`** | URL Decode selection | Decodes percent-encoded text back to plain text. |
| **`Ctrl + B`** | Base64 Encode selection | Instantly turns a string into Base64 format. |
| **`Ctrl + Shift + B`** | Base64 Decode selection | Decodes a Base64 string (great for checking session cookies). |
| **`Ctrl + Space`** | Toggle Intercept | Instantly turns Burp Intercept ON or OFF without clicking. |

---

## 🚀 3. Advanced Power Extensions (BApp Store)

Install these extensions inside Burp to automate heavy WPT tasks:

* **Autorize:** * *Why:* Essential for **A01: Broken Access Control**. 
    * *How it works:* You feed it a low-privilege user's cookie, and as you browse the site as an Admin, Autorize automatically repeats those requests with the low-privilege cookie to see if access control is broken.
* **Param Miner:**
    * *Why:* Finds hidden or unlinked parameters that don't show up in the UI (great for finding web cache poisoning or hidden administrative flags).
* **Logger++:**
    * *Why:* Provides a much better, color-coded, filterable view of all requests made by Burp tools (including Intruder and Scanner background traffic).

