#  John the Ripper (JtR) 🔑

John the Ripper (JtR) is a free, open-source, and incredibly powerful offline password cracker. While it is simple to use, understanding its underlying structure—especially **Formats** and **Modes**—is the key to becoming an effective ethical hacker.

---

## 📌 Table of Contents
1. [Core Concepts & Architecture](#-core-concepts--architecture)
2. [Preparing Hashes (Unshadowing)](#-preparing-hashes-unshadowing)
3. [The Importance of Formats](#-the-importance-of-formats)
4. [Cracking Modes](#-cracking-modes)
5. [Managing Sessions](#-managing-sessions)
6. [Advanced Tips for CEH Labs](#-advanced-tips-for-ceh-labs)

---

## 🧠 Core Concepts & Architecture
John the Ripper doesn't just "guess" passwords; it follows a specific, configurable logic. It works by taking an encrypted hash and comparing it against the hashed versions of words from a dictionary or generated patterns.



---

## 📂 Preparing Hashes (Unshadowing)
In Linux systems, the password hashes are stored in `/etc/shadow`, which is readable only by `root`. To crack them, you first need to merge the `/etc/passwd` (user info) and `/etc/shadow` (hashes) files.

**The `unshadow` command:**
```
unshadow /path/to/passwd /path/to/shadow > hashes.txt
## 🛠️ Advanced Tool Usage: John the Ripper (Formats)

By default, John the Ripper khud guess karta hai ki hash kis type ka hai. Lekin kai baar MD5 aur NTLM dekhne mein ek jaise lagte hain (dono 32-character ke hote hain). Agar John galat guess karega, toh password kabhi crack nahi hoga. Isliye best practice hai ki humesha `--format=` flag ka use karein. ### Finding Supported Formats John kaun kaun se formats support karta hai, yeh dekhne ke liye:

john --list=formats
```

## 🛠️ The Importance of Formats (`--format`)

As discussed, John attempts to "auto-detect" the hash type. However, for complex hashes, auto-detection often fails. **Always specify the format manually to save time and ensure accuracy.**

**List all supported formats:**

Bash

```
john --list=formats
```

**Common Hash Formats used in Penetration Testing:**

|**Target System**|**Hash Type**|**John --format Flag**|
|---|---|---|
|**Windows**|NTLM|`--format=NT`|
|**Linux/Unix**|SHA-512 crypt|`--format=sha512crypt`|
|**Linux/Unix**|MD5 crypt|`--format=md5crypt`|
|**Standard**|Raw MD5|`--format=Raw-MD5`|
|**Standard**|Raw SHA-1|`--format=Raw-SHA1`|
|**PDF Files**|PDF Document|`--format=pdf`|
|**Zip Files**|Zip Archive|`--format=zip`|

## ⚡ Cracking Modes

John offers three primary modes of operation to cover different cracking scenarios:

### 1. Single Crack Mode (`--single`)

This is the fastest mode. It uses the user's login information (Username, Full Name, Home Directory) to derive possible passwords.

- _Example:_ If the user is "john" and his name is "John Doe", it will try `john`, `John`, `johndoe`, `john123`, etc.
    
- **Usage:** `john --single hashes.txt`
    

### 2. Wordlist Mode (`--wordlist`)

This is the standard mode where you provide a dictionary file (like `rockyou.txt`). John hashes every word in the dictionary and compares it to the target hash.

- **Usage:** `john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt`
    

### 3. Incremental Mode (`--incremental`)

This is essentially a brute-force mode. It tries every possible combination of characters. It is the slowest mode but ensures that if the password exists, it **will** be found.

- **Usage:** `john --incremental hashes.txt`
    

## 💾 Managing Sessions

Cracking can take hours or even days. If you close your terminal, the process stops. John automatically saves its state so you can resume later.

- **To Pause:** Press `Ctrl + C` (John saves the state automatically).
    
- **To Resume:**

    ```
    john --restore
    ```
    

## 📋 Cheat Sheet for CEH Labs

- **View cracked passwords:** `john --show hashes.txt`
    
- **Use rules (to mutate words):**
    
    `john --wordlist=rockyou.txt --rules hashes.txt`
    
- **Filter by user:**
    
    `john --users=root hashes.txt`
    