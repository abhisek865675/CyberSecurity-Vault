# 03: Logical Bypasses & Defensive Coding 🛡️

SQL Injection isn't just about dumping tables; it's often used to completely bypass application logic. This file covers how attackers exploit SQLi for authentication bypass and, more importantly, how developers must code to stop injection permanently.

---

## 📌 Table of Contents
1. [Authentication Bypass Techniques](#1-authentication-bypass-techniques)
2. [Advanced Logic Manipulation](#2-advanced-logic-manipulation)
3. [The Permanent Fix: Prepared Statements](#3-the-permanent-fix-prepared-statements)
4. [Defense Checklist](#4-defense-checklist)

---

## 1. Authentication Bypass Techniques

Authentication bypass is the "Golden Ticket." It allows an attacker to log in as any user (usually the Administrator) without knowing a single password.

### The Mechanism
Most login forms use a query similar to this:
`SELECT id, username FROM users WHERE username = '$user' AND password = '$password';`



### Exploitation Payloads:
* **The `OR 1=1` Method:** Inputting `' OR '1'='1` into the password field changes the logic to:
  `WHERE username = 'admin' AND password = '' OR '1'='1';`
  Since `'1'='1'` is always true, the database returns a result for the Admin user regardless of the password.
* **The Comment Method:** Using `--` or `#` (in MySQL) to comment out the entire password check:
  `admin' -- `
  The query effectively becomes:
  `WHERE username = 'admin'; -- ' AND password = '...'`
  The password check is ignored entirely.

---

## 2. Advanced Logic Manipulation

Attackers don't just stop at login pages. They look for SQLi in business logic modules:

* **Password Reset Bypasses:** If an app sends a password reset token, an attacker can use SQLi to modify the `reset_token` column in the database to a value they already know, effectively hijacking the reset process.
* **Privilege Escalation:** If the application checks user roles using a query like `SELECT role FROM users WHERE id=$user_id`, an attacker can use `UNION SELECT` to force the application to believe their `role` is `admin`.

---

## 3. The Permanent Fix: Prepared Statements

Prepared Statements (also known as **Parameterized Queries**) are the only way to stop SQLi once and for all.

### Why They Work:
In a normal query, the database engine receives the code and the data mixed together. In a Prepared Statement, the database engine receives the **SQL code structure first**. The user input is then sent separately as a "parameter."

The database *never* executes the input as code. Even if a user enters `' OR 1=1 --`, the database will simply look for a user whose literal username is the string `"' OR 1=1 --"`.

### Example (PHP/PDO):
```php
// VULNERABLE CODE (Don't do this)
$sql = "SELECT * FROM users WHERE username = '" . $username . "'";

// SECURE CODE (Use Prepared Statements)
$stmt = $pdo->prepare('SELECT * FROM users WHERE username = :username');
$stmt->execute(['username' => $username]);
$user = $stmt->fetch(); 
```

## 4. Defense Checklist

If you are building an application or auditing one, use this checklist to ensure SQLi is impossible:

- [ ] **Use Prepared Statements:** Always use them for all database interactions.
    
- [ ] **Principle of Least Privilege:** Ensure the database user account that the web app uses has only the permissions it needs. It should never be `DBA` (root/admin).
    
- [ ] **Input Validation:** Use allow-lists. If a field expects a number, reject anything that isn't a number.
    
- [ ] **Disable Verbose Errors:** Never display database errors (like SQL syntax errors) to the end user. Log them to a private file instead.
    
- [ ] **Use WAFs:** A Web Application Firewall acts as a filter to block common SQLi payloads (like `UNION SELECT`), adding an extra layer of defense for legacy code that cannot be easily updated.


---