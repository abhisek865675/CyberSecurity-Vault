# OWASP Top 10 - Injection

## Overview
Injection flaws occur when an application sends untrusted data to an interpreter (SQL, OS command, LDAP, etc.) as part of a command or query. The attacker’s hostile data tricks the interpreter into executing unintended commands or accessing unauthorized data.

> **Key Takeaway:** The vulnerability lies in the application failing to distinguish between **Data** (what the user types) and **Code** (what the server executes).

---

## 1. Common Types of Injection
* **SQL Injection (SQLi):** Attacker inserts malicious SQL code into input fields to manipulate database queries.
* **Command Injection:** Attacker executes arbitrary OS commands on the server.
* **Cross-Site Scripting (XSS):** Attacker injects malicious scripts (JavaScript) into web pages viewed by other users.
* **LDAP/XML Injection:** Manipulating directory services or XML parsers.

---

## 2. The Hacker's "Mindset" (Methodology)
To test for injection, focus on these steps:

1.  **Fuzzing/Testing:** Identify all input fields (Search bars, login forms, URL parameters, headers). 
2.  **Character Injection:** Inject special characters (`'`, `"`, `;`, `--`, `|`, `&`, `<script>`) to see how the application reacts.
3.  **Observation:** * Do you get an error message? (Error-based injection).
    * Does the page take longer to load? (Blind injection).
    * Does the content change? (Boolean-based or reflected injection).
4.  **Payload Delivery:** Once you identify the "injection point," craft a payload to achieve your goal (Dump the DB, get a reverse shell, or steal a session).

---

## 3. Prevention (The Fix)
* **Parameterized Queries (Prepared Statements):** The #1 defense. This ensures the database treats input as **data only**, never as executable code.
* **Input Validation:** Use "Allow-lists" (e.g., only accept alphanumeric characters in a username field).
* **Escaping/Sanitization:** If you must use user input in a command, sanitize it to remove special characters that could trigger an injection.
* **Principle of Least Privilege:** Don't run your database or web app as `root` or `admin`.

---
