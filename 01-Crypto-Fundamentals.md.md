# Crypto Fundamentals 🧬

Cryptography is the foundation of information security, providing mathematical guarantees that protect data at rest, in transit, and in use.



---

## 📌 Table of Contents
1. [The Cryptographic Triad & Beyond](#1-the-cryptographic-triad--beyond)
2. [The Core Distinction: Encoding vs. Encryption vs. Hashing](#2-the-core-distinction-encoding-vs-encryption-vs-hashing)
3. [Key Management: Symmetric vs. Asymmetric](#3-key-management-symmetric-vs-asymmetric)
4. [Cipher Types](#4-cipher-types)
5. [Common Cryptographic Attacks](#5-common-cryptographic-attacks)

---

## 1. The Cryptographic Triad & Beyond
Cryptography provides five essential security services:
* **Confidentiality:** Ensures only authorized parties can access information.
* **Integrity:** Guarantees data has not been altered (using hashing/checksums).
* **Availability:** Ensures systems/data are accessible when needed.
* **Authentication:** Verifies the identity of users and systems (using digital signatures).
* **Non-Repudiation:** Ensures a sender cannot deny sending a message.

---

## 2. The Core Distinction: Encoding vs. Encryption vs. Hashing
It is critical to understand that these three serve different purposes. Misusing them is a primary cause of security vulnerabilities.



| Feature | Encoding | Encryption | Hashing |
| :--- | :--- | :--- | :--- |
| **Purpose** | Data Formatting | Confidentiality | Integrity Verification |
| **Security** | None | High (if implemented well) | Integrity Only |
| **Key Required?** | No | Yes (Secret Key) | No |
| **Reversible?** | Yes | Yes (with correct key) | No (One-way) |
| **Example** | Base64, ASCII | AES, RSA | SHA-256, MD5 |

* **Encoding:** Used for data representation and compatibility. It is **not** security. Anyone with the algorithm can decode it.
* **Encryption:** Transforms plaintext to ciphertext. The goal is to hide content, which can be recovered later with the correct key.
* **Hashing:** Creates a unique "fingerprint" (digest). Changing even one bit of the input changes the entire hash. It is mathematically impossible to reverse.

---

## 3. Key Management: Symmetric vs. Asymmetric
Cryptography relies on keys. How you handle them determines your security posture.



* ### Symmetric Encryption:
    * Uses a **single shared key** for both encryption and decryption.
    * **Pros:** Very fast and efficient for bulk data.
    * **Cons:** Key distribution is difficult (how do you share the secret key safely?).
    * **Algorithm:** AES (Advanced Encryption Standard).

- **Advantages:**
    
    - **Speed:** Requires significantly less processing power. Perfect for encrypting large amounts of data (like a 10GB hard drive).
        
    - **Simplicity:** Easier to implement at the hardware level.
        
- **Disadvantages:**
    
    - **Key Distribution:** This is the "Key Distribution Problem." If you want to send encrypted data to a friend, how do you give them the password without an attacker stealing it in transit?
        
    - **Scalability:** If you have 1,000 users, you need 1,000 separate secret keys.
        

### Asymmetric Encryption

* Uses a **key pair**: A **Public Key** (to encrypt) and a **Private Key** (to decrypt).
    * **Pros:** Secure key exchange; no need to share the private key.
    * **Cons:** Slower, computationally expensive.
    * **Algorithm:** RSA, ECC (Elliptic Curve Cryptography).

- **Advantages:**
    
    - **Security:** You never have to share your "Private Key." You only share your "Public Key."
        
    - **Scalability:** Much easier to manage in large networks (like the entire internet).
        
- **Disadvantages:**
    
    - **Performance:** It is mathematically heavy. Encrypting large files directly with asymmetric encryption is too slow for modern standards.

## Symmetric vs. Asymmetric Keys

The fundamental difference lies in how many keys are used and how they are handled.

|**Feature**|**Symmetric Keys**|**Asymmetric Keys (Public/Private)**|
|---|---|---|
|**Number of Keys**|One (Shared Secret)|Two (Pair: Public & Private)|
|**Key Distribution**|Difficult (Must be sent securely)|Easy (Public key is shared freely)|
|**Speed**|Extremely Fast (Low overhead)|Slow (High mathematical complexity)|
|**Security**|Depends on keeping the secret safe|Depends on keeping the Private key safe|
|**Usage**|Bulk data encryption (e.g., Disk/File)|Key exchange, Digital signatures, HTTPS|


---
## 4. Modern Key Exchange: The "Hybrid" Approach

You asked what happens in "modern key exchange." In the real world (like HTTPS/TLS), we **never** choose just one. We combine them. This is called **Hybrid Cryptography**.

### The Problem

We want the _security_ of Asymmetric (easy key distribution) but the _speed_ of Symmetric (fast data transfer).

### The Solution: The "Handshake"

When you visit a website (e.g., `google.com`), your browser and the server perform a **TLS Handshake** to establish a secure session:

1. **Asymmetric Phase (The Setup):** The browser uses the server's **Public Key** (or a mathematical protocol called **Diffie-Hellman**) to establish a shared secret _without_ actually sending the secret itself over the wire.
    
2. **Symmetric Phase (The Data):** Once both sides have agreed on a "Session Key" (a temporary symmetric key), the asymmetric encryption is turned off. All your actual traffic (videos, text, passwords) is encrypted using **Symmetric (AES)** encryption.
    

### What is Diffie-Hellman (DH)?

DH is the genius protocol behind modern key exchange.

- **The Logic:** Alice and Bob generate their own private numbers and public numbers. They exchange the public parts. Using the math of discrete logarithms, both Alice and Bob can "calculate" the _same_ secret key on their own machines, _without_ the secret ever being transmitted over the network.
    
- **Result:** Even if an attacker captures all the public numbers exchanged, they cannot calculate the final secret key.
    

> **💡 Professional Analysis:** If you are designing a system, follow this rule:
> 
> 1. Use **Asymmetric** (RSA/ECC) to establish the connection and authenticate the server.
>     
> 2. Use that connection to exchange a random **Symmetric** (AES) key.
>     
> 3. Use that **Symmetric** key for all the data movement.


---

## 5. Cipher Types
* **Block Ciphers:** Encrypt data in fixed-size blocks (e.g., AES-128). It is the most common for bulk data.
* **Stream Ciphers:** Encrypt data bit-by-bit or byte-by-byte. They are faster and often used for real-time communication.

---

## 6. Common Cryptographic Attacks
Cryptography usually fails due to **implementation errors** rather than mathematical weaknesses.

* **Brute-Force Attacks:** Systematically trying all possible keys until the correct one is found.
* **Dictionary Attacks:** Using precomputed lists of common passwords/phrases.
* **Hash Collisions:** Finding two different inputs that produce the same hash output (weakens integrity).
* **Poor Key Management:** Hardcoding keys in source code, weak storage, or lack of key rotation.

---

