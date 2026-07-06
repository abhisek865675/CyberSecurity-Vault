# 02: SQLMap Automation 🛠️

`sqlmap` is the industry-standard tool for automating the detection and exploitation of SQL injection vulnerabilities. It handles the heavy lifting—detecting the database type, identifying injection points, and exfiltrating data—using its own internal library of thousands of known payloads.



---

## 📌 Table of Contents
1. [Core Features & Capability](#1-core-features--capability)
2. [The Basic Workflow](#2-the-basic-workflow)
3. [Commonly Used SQLMap Commands](#3-commonly-used-sqlmap-commands)
4. [Evasion & WAF Bypass Strategies](#4-evasion--waf-bypass-strategies)

---

## 1. Core Features & Capability
* **Automated Detection:** Automatically detects if a parameter is vulnerable to In-Band, Boolean, or Time-based SQLi.
* **Database Fingerprinting:** Identifies the backend database (MySQL, Oracle, PostgreSQL, MS SQL Server, etc.) by analyzing response fingerprints.
* **Extensive Dumping:** Once the injection is confirmed, it can dump the entire database structure, user tables, and passwords in a single command.
* **OS Interaction:** If the database permissions are high enough, it can upload a file, download a file, or even spawn a full reverse shell.

---

## 2. The Basic Workflow

Testing a site with SQLMap usually follows a 4-step lifecycle:

1. **Targeting:** Identify the URL and the specific parameter.
2. **Detection:** Ask SQLMap to check if the parameter is vulnerable.
3. **Enumeration:** Ask SQLMap to list databases, tables, and columns.
4. **Exfiltration:** Ask SQLMap to dump the content of specific tables.

```bash
# 1. Detection
sqlmap -u "[http://target.com/product.php?id=1](http://target.com/product.php?id=1)" --batch

# 2. List Databases
sqlmap -u "[http://target.com/product.php?id=1](http://target.com/product.php?id=1)" --dbs

# 3. List Tables (after finding the DB name)
sqlmap -u "[http://target.com/product.php?id=1](http://target.com/product.php?id=1)" -D database_name --tables

# 4. Dump Data
sqlmap -u "[http://target.com/product.php?id=1](http://target.com/product.php?id=1)" -D database_name -T users --dump
```

## 3. Commonly Used SQLMap Commands

|**Command**|**Function**|
|---|---|
|`-u`|Specifies the target URL.|
|`--data="post_data"`|Used for POST requests (injecting into form fields).|
|`--batch`|Automatically chooses "Yes" for all prompts (faster).|
|`--level 5 --risk 3`|Increases intensity. Level 5 checks headers/cookies; Risk 3 includes heavy time-based tests.|
|`--proxy`|Routes traffic through Burp Suite (Crucial for monitoring the tool).|
|`--os-shell`|Attempts to get a shell on the underlying OS.|

## 4. Evasion & WAF Bypass Strategies

Modern Web Application Firewalls (WAFs) are designed to kill SQLMap activity instantly. To bypass them, you must use **Tamper Scripts**. These scripts modify the payload dynamically so the WAF doesn't recognize the signature.

- **Example of a WAF Bypass:**
    
    Bash
    
    ``` bash
    sqlmap -u "[http://target.com/product.php?id=1](http://target.com/product.php?id=1)" --tamper=between,randomcase --level 5
    ```
    
    - `between`: Replaces operators like `>` with `NOT BETWEEN`.
        
    - `randomcase`: Replaces `SELECT` with `SeLeCt`, which often confuses legacy WAF filters.
        

> **⚠️ Professional Warning:**
> 
> `sqlmap` is a loud tool. It will send hundreds, sometimes thousands of requests to the target server in minutes. If you are not in a controlled lab (like DVWA) or a sanctioned Bug Bounty program, **do not use this on live production systems**. You will trigger IDS/IPS alerts immediately and get your IP blacklisted by the WAF. Always monitor your traffic through Burp Suite to see exactly what `sqlmap` is doing under the hood.


---
