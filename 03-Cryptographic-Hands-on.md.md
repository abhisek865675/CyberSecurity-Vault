#  Cryptographic Hands-on Labs 🛠️

These are the commands you need for the CEH practical scenarios.

## 1. Steganography (Hiding data in images)
* **Install:** `sudo apt-get install steghide`
* **Embed data:** `steghide embed -cf image.jpeg -ef secret.txt`
* **Extract data:** `steghide extract -sf image.jpeg`

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

## 5. CyberChef
* **Usage:** Use it for Base64 decoding, format conversions, and testing encryption blocks without coding.
* **Scenario:** Always check if input is "Raw" or "Hex" before running operations.