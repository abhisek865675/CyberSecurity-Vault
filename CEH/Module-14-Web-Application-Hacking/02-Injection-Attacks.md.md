# 02: Injection Attacks (SQLi & OS Command Injection) 💉

Injection is one of the oldest, most dangerous, and most prevalent vulnerabilities on the internet. It occurs when an application takes untrusted user input and sends it directly to a backend interpreter (like a database or an operating system shell) without proper sanitization. 



---

## 📌 Table of Contents
1. [The Core Concept: Data vs. Code](#1-the-core-concept-data-vs-code)
2. [SQL Injection (SQLi) Breakdown](#2-sql-injection-sqli-breakdown)
3. [OS Command Injection](#3-os-command-injection)
4. [Defensive Countermeasures](#4-defensive-countermeasures)

---

## 1. The Core Concept: Data vs. Code

To understand injection, you must understand how applications talk to backend systems.
When a developer writes code, they expect the user's input to be treated purely as **Data** (e.g., a name, an email, or a search term). 

An **Injection Attack** happens when the attacker crafts their input using special syntax characters (like `'`, `"`, `;`, or `--`). These characters trick the backend interpreter into breaking out of the data context and treating the attacker's input as executable **Code**.

---

## 2. SQL Injection (SQLi) Breakdown

SQL Injection targets the backend database (MySQL, PostgreSQL, MSSQL). It allows an attacker to read, modify, or delete data they are not authorized to access.

### The Classic Authentication Bypass
Imagine a backend PHP script checking a login form:
```sql
SELECT * FROM users WHERE username = '$user_input' AND password = '$password_input'; 
```
If an attacker enters `' OR 1=1 --` into the username field, the query becomes:

SQL

```
SELECT * FROM users WHERE username = '' OR 1=1 --' AND password = '';
```

- **The `'` (Single Quote):** Closes the developer's original data string early.
    
- **The `OR 1=1`:** Creates a mathematical condition that is always true.
    
- **The `--` (Double Dash):** Comments out the rest of the query (ignoring the password check entirely).
    
- **Result:** The database returns the first user in the table (usually the Admin) and logs the attacker in without a password.
    

### 🧩 Types of SQL Injection

|**SQLi Type**|**How It Works**|**Real-World Scenario**|
|---|---|---|
|**In-Band (Error-Based)**|Forces the database to generate an error message on the web page.|The error message explicitly leaks the database version or table names.|
|**In-Band (UNION-Based)**|Uses the `UNION` operator to combine the results of the original query with a malicious query.|Extracting passwords from a completely different table and displaying them on the search page.|
|**Inferential (Blind Boolean)**|The application doesn't print data or errors. The attacker asks the database True/False questions.|Injecting `AND 1=1` (page loads normally) vs `AND 1=2` (page loads blank) to slowly guess data character by character.|
|**Inferential (Blind Time-Based)**|The attacker forces the database to pause before responding.|Injecting `SLEEP(10)`. If the web page takes exactly 10 seconds to load, the vulnerability is confirmed.|

## 3. OS Command Injection

While SQLi targets the database, **OS Command Injection** targets the underlying server operating system. This happens when a web app passes user input directly into a system shell (e.g., using functions like `system()`, `exec()`, or `os.popen()`).

### The Mechanics

Imagine a web tool that lets you ping an IP address to see if it is alive:

Bash

```
ping -c 4 $user_input
```

An attacker can use shell meta-characters (Command Separators) to chain their own malicious commands onto the end of the legitimate input.

### Common Shell Meta-Characters:

- **`;` (Semicolon):** Executes the first command, then executes the second.
    
- **`&&` (AND):** Executes the second command _only_ if the first one succeeds.
    
- **`|` (Pipe):** Takes the output of the first command and feeds it into the second.
    

**The Attack Payload:**

If the attacker enters `8.8.8.8 ; cat /etc/passwd`, the server executes:

Bash

```
ping -c 4 8.8.8.8 ; cat /etc/passwd
```

- **Result:** The server pings Google, and then immediately prints the contents of the server's password file directly to the web page.
    

## 4. Defensive Countermeasures

Injection vulnerabilities are entirely preventable with proper coding practices.

### A. Parameterized Queries (Prepared Statements) - For SQLi

This is the only 100% effective defense against SQL Injection. Prepared statements force the application to define the exact SQL code structure _before_ inserting the user data. The database treats the user input strictly as a literal value, meaning special characters like `'` or `OR` lose all syntactic meaning and cannot alter the query logic.

### B. Input Validation & Sanitization - For Command Injection

- **Allow-listing (Positive Validation):** Instead of trying to block bad characters (which attackers constantly bypass), developers should only allow strictly known-good input. For an IP address field, use a Regex filter that _only_ accepts numbers and periods. Reject everything else.
    
- **Avoid System Calls:** Developers should avoid calling OS commands directly from the application code. Instead, use native language libraries (e.g., using Python's `os.mkdir()` instead of `os.system("mkdir " + folder_name)`).
    

> **💡 Red Team Perspective:**
> 
> SQLMap is the industry standard tool for automating SQL injection discovery and exploitation. However, blindly running `sqlmap -u target.com` is loud and easily caught by Web Application Firewalls (WAFs). Elite testers use Burp Suite to manually find the injection point, craft a custom tamper script, and then feed that specific request into SQLMap to extract the data stealthily.


---
