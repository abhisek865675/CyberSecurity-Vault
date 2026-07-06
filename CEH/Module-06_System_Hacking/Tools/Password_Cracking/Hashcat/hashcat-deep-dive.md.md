# Deep Dive: Hashcat (The World's Fastest Password Cracker) ⚡

Hashcat is the advanced, multi-threaded password recovery utility that utilizes the power of your GPU (Graphics Processing Unit) to perform high-speed cracking. Unlike CPU-based tools, Hashcat's ability to leverage the parallel processing power of graphics cards makes it the industry standard for professional penetration testing.

---

## 📌 Table of Contents
1. [What is Hashcat?](#-what-is-hashcat)
2. [Basic Syntax](#-basic-syntax)
3. [Algorithm/Hash Modes (-m)](#-algorithmhash-modes--m)
4. [Attack Modes (-a) Detailed](#-attack-modes--a-detailed)
5. [Advanced Feature: Rule-Based Attacks](#-advanced-feature-rule-based-attacks)
6. [Best Practices](#-best-practices)

---

## 📖 What is Hashcat?
Hashcat is an offline password recovery tool. Its primary advantage is speed. While JtR is great for quick, CPU-based tasks, Hashcat is designed to handle millions of hashes per second by offloading the mathematical workload to your GPU. 



---

## 💻 Basic Syntax
The standard command structure for Hashcat is:
> `hashcat -m [Hash-Mode] -a [Attack-Mode] [Hash-File] [Wordlist/Mask]`

* **-m:** Specifies the hash type (e.g., MD5, NTLM, SHA-256).
* **-a:** Specifies the attack mode (how to crack).

---

## 🔍 Identifying Hash Modes (-m)
Hashcat supports hundreds of algorithms. You must specify the correct ID using the `-m` flag.

* **To list all modes:** `hashcat --help | grep -i "hash modes"`
* **Commonly used modes:**
  * `0` : MD5
  * `1000` : NTLM
  * `1800` : sha512crypt (Linux)
  * `1710` : sha256crypt (Linux)

---

## ⚔️ Attack Modes (-a) Detailed

### 1. Straight Mode (`-a 0`) - Dictionary Attack
This is the most common mode. It takes a file containing a list of passwords (wordlist) and tries each word against the target hash.
* **Why it works:** Many users use predictable passwords (e.g., `password123`, `qwerty`).
* **Example:** `hashcat -m 0 -a 0 hashes.txt rockyou.txt`

### 2. Combinator Mode (`-a 1`)
This mode combines two different wordlists to create complex passwords.
* **Why it works:** Users often combine two simple words (e.g., `cat` + `dog` = `catdog`).
* **Example:** `hashcat -m 0 -a 1 hashes.txt wordlist1.txt wordlist2.txt`

### 3. Brute-Force / Mask Mode (`-a 3`)
Instead of a wordlist, you use a "Mask" (pattern). Hashcat generates every possible combination based on the pattern you define.
* **Mask Characters:** `?l` (a-z), `?u` (A-Z), `?d` (0-9), `?s` (special chars), `?a` (all).
* **Example (4-digit PIN):** `hashcat -m 0 -a 3 hashes.txt ?d?d?d?d`

### 4. Hybrid Mode (`-a 6` and `-a 7`)
These combine a dictionary with a mask.
* **-a 6 (Wordlist + Mask):** Takes a dictionary word and adds a pattern at the end (e.g., `password` + `1999` = `password1999`).
* **-a 7 (Mask + Wordlist):** Adds a pattern to the beginning (e.g., `!` + `admin` = `!admin`).

---

## 🧠 Advanced Feature: Rule-Based Attacks
Rule-based attacks allow you to modify dictionary words on-the-fly. Hashcat includes pre-defined rules in the `/rules/` folder.

* **How it works:** If you have the word `password` in your wordlist, and you apply a rule, Hashcat will try `Password`, `PASSWORD`, `p4ssw0rd`, etc., without you having to manually write them in the wordlist.
* **Usage:** `hashcat -m 0 -a 0 -r rules/best64.rule hashes.txt wordlist.txt`

---

## 📋 Best Practices
1. **Target the Right GPU:** Hashcat performs best when it detects your dedicated GPU (NVIDIA/AMD).
2. **Use `-O` (Optimized Kernel):** If your hash length is short, use the `-O` flag to get a significant speed boost.
3. **Session Management:** Always use `--session [name]` to save progress, so you can resume later with `--restore`.

---

⚠️ **Disclaimer:** This guide is for educational purposes only. Unauthorized password cracking is illegal. Always test on systems you own or have explicit permission to audit.