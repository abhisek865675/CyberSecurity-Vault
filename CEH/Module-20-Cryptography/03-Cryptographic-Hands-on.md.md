#  Cryptographic Hands-on Labs 🛠️

These are the commands you need for the CEH practical scenarios.

## 1. Steganography (Hiding data in images)
* **Install:** `sudo apt-get install steghide`
* **Embed data:** `steghide embed -cf image.jpeg -ef secret.txt`
* **Extract data:** `steghide extract -sf image.jpeg`
* If the file is password protected : `stegcrack secret_photo.jpg /usr/share/wordlists/rockyou.txt`
## 2. AES Encryption (OpenSSL)
* **Encrypt:** `openssl enc -aes-256-cbc -salt -in p.txt -out p.txt.aes -pass pass:yourpassword`
* **Decrypt:** `openssl enc -d -aes-256-cbc -in p.txt.aes -out p.txt -pass pass:yourpassword`

## 3. RSA Key Generation
* **Generate RSA Key:** `openssl genrsa -des3 -out ca.key 4096`
    * `-genrsa`: Generates the key.
    * `-des3`: Encrypts the key file itself with a password.
    * `4096`: Very secure bit-length.

## 4. Hashing & Integrity
* **MD5:** `md5sum file.txt`
* **SHA-256:** `sha256sum file.txt`
* **Check Integrity:** If you hash a file, change it, and hash it again, the result will be different. This proves tampering.
* **Identify Hash Type:** Use `hash-identifier` (a common tool in Kali) to detect if you have an MD5 or SHA hash.

# Password Hashing Tools: OpenSSL vs mkpasswd 🔐

Password hashing is not just about converting text to a string; it is about using algorithms that are "slow" enough to resist brute-force attacks and "salted" to prevent rainbow table attacks.

---

## i. OpenSSL (General Purpose Crypto Tool)
OpenSSL ek general-purpose tool hai. Ye kisi bhi data ko hash kar sakta hai, na ki sirf passwords ko.

##### OpenSSL Hashing Syntax 🛠️

To calculate the hash of any file or text using OpenSSL, we use the `dgst` (digest) command.

---

## How to view available Hash Algorithms
If you want to see which hashing algorithms (e.g., sha256, md5, sha512) your system supports, run the following command:

```bash
openssl list -digest-algorithms
```
_Note: On older OpenSSL versions, you might need to use:_


``` bash
openssl dgst -list
```

This will display all the algorithms available for use on your machine.

##  OpenSSL Hashing Syntax

The general syntax for the `dgst` command is as follows:

### A. To hash a file:


```  bash
openssl dgst -[hash_type] [filename]
```

_Example (SHA-256):_


``` bash
openssl dgst -sha256 myfile.txt
```

### B. To hash a string (Text):

When hashing direct text, use the `echo` command.

**Important:** The `-n` flag is crucial because it prevents `echo` from adding an extra newline character to the end of the text. If the newline character is included, the resulting hash will be incorrect.


``` bash
echo -n "your_text_here" | openssl dgst -sha256
```

## 3. Quick Reference Table

|**Algorithm**|**Flag**|**Status**|
|---|---|---|
|**SHA-256**|`-sha256`|Secure (Recommended)|
|**SHA-512**|`-sha512`|Very Secure|
|**MD5**|`-md5`|Insecure (Use only for non-security tasks)|
|**SHA-1**|`-sha1`|Deprecated (Avoid)|

---
# ii. mkpasswd Utility 🔐

While `openssl` is great for general cryptography, `mkpasswd` is a specialized tool built specifically for generating **password hashes**. It is widely used for creating user passwords in Linux environments.



---

## Why we use mkpasswd?
Unlike general hashing tools, `mkpasswd` handles the **Salt** automatically. 
* **Salt:** A random piece of data added to the password before hashing. 
* **Why it matters:** Even if two users have the same password, their hashes will look completely different because of the salt. This prevents "Rainbow Table" attacks.

## Installation
`mkpasswd` is usually part of the `whois` or `expect` packages on Linux. If it is not found, install it using:
```bash
sudo apt-get install whois
```

## Syntax and Usage

### A. Basic Syntax

Bash

```
mkpasswd -m [algorithm]
```

### B. Example (Recommended - SHA-512)

SHA-512 is the current industry standard for local Linux password hashing.

Bash

```
mkpasswd -m sha-512
```

_After running this, the terminal will prompt you to enter the password, and then it will display the hashed version._

### C. Direct/Non-Interactive (For scripts)

If you want to pass the password directly without a prompt:

Bash

```
mkpasswd -m sha-512 "your_password_here"
```

##  Advantages & Disadvantages

| **Feature**    | **Advantage**                                              | **Disadvantage**                                          |
| -------------- | ---------------------------------------------------------- | --------------------------------------------------------- |
| **Security**   | Automatically adds a **Salt** to the hash.                 | Not installed by default on all systems.                  |
| **Use Case**   | Perfect for system user accounts.                          | Not suitable for general file hashing.                    |
| **Simplicity** | Designed to work directly with Linux `/etc/shadow` format. | Limited configuration options compared to `openssl`.      |
| **Algorithms** | Supports modern, slow algorithms (bcrypt, sha-512).        | Does not support custom complex cryptographic parameters. |


---
