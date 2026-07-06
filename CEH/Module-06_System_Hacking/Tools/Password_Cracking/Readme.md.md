# Lab 01: Password Cracking & Hash Analysis 🔑

Welcome to the **Password Cracking** guide. In the System Hacking phase, once you extract user databases or intercept authentication traffic, you rarely get cleartext passwords. Instead, you get **Hashes**. Your job is to "crack" these hashes to reveal the original password.

---

## 📌 Table of Contents
1. [Understanding Hashes & Salts](#-understanding-hashes--salts)
2. [Types of Password Attacks](#-types-of-password-attacks)
3. [Extracting Hashes (Where do they live?)](#-extracting-hashes-where-do-they-live)

---

## 🧠 Understanding Hashes & Salts

* **Hashing:** A one-way mathematical function. You can convert a password into a hash (e.g., `admin123` -> `0192023a7bbd73250516f069df18b500`), but you **cannot** decrypt a hash back into a password. To crack it, we have to guess the password, hash our guess, and see if the two hashes match.
* **Salting:** A random string of characters added to a password before hashing. This prevents attackers from using pre-computed tables (Rainbow Tables) to crack passwords instantly.

---

## ⚔️ Types of Password Attacks

1. **Dictionary Attack:** Uses a massive text file (wordlist) of common passwords (like the famous `rockyou.txt`). It tries every word in the list. This is the fastest and most common method.
2. **Brute-Force Attack:** Tries every possible combination of letters, numbers, and symbols (e.g., `aaaa`, `aaab`, `aaac`). It takes a massive amount of time and computing power.
3. **Rule-Based Attack:** Takes a dictionary word and applies rules to it (e.g., taking the word `password`, capitalizing the first letter, and adding `123` to the end -> `Password123`).

---

## 📂 Extracting Hashes (Where do they live?)

Before you can crack a password, you need the hash. Here is where operating systems hide them:

### Linux (Shadow File)
Linux stores user hashes in the `/etc/shadow` file, which is only readable by the `root` user.
* *Example Hash:* `$6$salthex$hashedstring...` (The `$6$` indicates it is a SHA-512 hash).

### Windows (SAM Database)
Windows stores local user hashes in the **Security Account Manager (SAM)** database.
* *Path:* `C:\Windows\System32\config\SAM`
* Windows uses **NTLM** (New Technology LAN Manager) hashes.

---

