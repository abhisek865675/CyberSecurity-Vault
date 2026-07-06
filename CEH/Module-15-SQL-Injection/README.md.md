# Module 15: SQL Injection (SQLi) Mastery 💉

SQL Injection (SQLi) is the technique of manipulating a web application's database queries to extract, modify, or delete unauthorized data. This module moves beyond the basics to focus on the systematic exploitation of backend databases using both manual techniques and advanced automation.

---

## 📌 Module Overview
1. **Understanding the Vulnerability:** How the application handles user input and where the database boundary breaks.
2. **Manual Exploitation:** Understanding the "why" behind the attack before using tools.
3. **Automated Exploitation:** Mastering `sqlmap` to perform massive data exfiltration efficiently.
4. **Logical Bypasses:** Using SQLi to bypass authentication and manipulate server-side business logic.

---

## 🏗️ Core Architecture: What is SQLi?
An SQL Injection occurs when an application takes user input and concatenates it directly into a database query string.



* **The Web App:** Takes input (e.g., `?id=1`).
* **The Query:** `SELECT name, desc FROM products WHERE id = 1`
* **The Injection:** If the app is insecure, an attacker can input `1 UNION SELECT username, password FROM users`.
* **The Result:** The database blindly executes the injected command, returning sensitive user credentials instead of product info.

---

## 🧠 The Three Pillars of SQLi Discovery

1. **Error-Based:** The server is misconfigured to reveal database errors directly in the response. If the error says "Syntax error near '...' ", you know exactly where to inject.
2. **Boolean-Based (Inferential):** The server returns no errors and no data. You inject logic like `AND 1=1` (page loads) vs `AND 1=2` (page fails). This confirms the backend is evaluating your logic.
3. **Time-Based (Inferential):** The most silent method. You force the database to `SLEEP()` for 5 seconds. If the page takes 5 seconds to load, you know you have total control over the database execution time.

---

## ⚠️ Warning
SQL Injection is one of the most destructive vulnerabilities. Testing should **only** be performed in controlled environments like:
* **DVWA (Damn Vulnerable Web Application)**
* **OWASP Juice Shop**
* **HackTheBox / TryHackMe labs**

