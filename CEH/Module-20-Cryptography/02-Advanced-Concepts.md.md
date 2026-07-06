# Advanced Concepts: Certificates & Signatures 📜

## 1. Digital Signatures
A digital signature ensures that a message was actually sent by the claimed sender (Non-repudiation) and hasn't been tampered with (Integrity).
* **Creation:** Sender uses their **Private Key** to sign a hash of the data.
* **Verification:** Receiver uses the Sender's **Public Key** to verify.
#  Digital Signatures & Trust Hierarchy ✒️

A digital signature is not just a file; it is a process that provides **Non-repudiation** (the sender cannot deny having signed the message), **Authentication**, and **Integrity**.

---

##  What is a Digital Signature and who creates it?
The purpose of a digital signature is to ensure the **Authenticity** (is the sender who they claim to be?) and **Integrity** (has the message been altered?).

* **The Process:**
  1. **Hashing:** The sender creates a hash of the message.
  2. **Signing:** The sender encrypts that hash using their **Private Key**. This encrypted hash is the "Digital Signature."
  3. **Verification:** The receiver uses the sender's **Public Key** to decrypt the signature and compares it with a newly generated hash of the received message.

### Who creates it?
  * **Sender:** Creates the signature to sign the document.
  * **Receiver:** Verifies the signature to ensure the document is legitimate.

---

##  CA and Sub-CA (The Trust Chain)

**If I create a signature myself, how do you trust me? This is where the Certificate Authority (CA) comes in.**

* **Root CA:** The highest level of "Trusted Entity" (e.g., DigiCert, Sectigo). They are self-signed. Their trust is pre-installed in browsers and operating systems worldwide.
* **Sub-CA (Intermediate CA):** Root CAs do not sign every website. They delegate authority to Sub-CAs.
  * **Chain of Trust:** Root CA -> signs Sub-CA -> Sub-CA signs the Website.
  * **Benefit:** If a Sub-CA is compromised, the Root CA can revoke it without destroying the entire trust system.

---

## 3. Key Concepts
* **Non-Repudiation:** The sender cannot claim "I did not sign this" because only they possess the Private Key.
* **Authentication:** The receiver is confirmed that the sender is the one holding the corresponding Private Key.


## 2. Certificates (PKI)
How do you know that a "Public Key" actually belongs to the person claiming it? You use a **Digital Certificate** signed by a trusted third party called a **Certificate Authority (CA)**. This is the foundation of HTTPS/SSL.

# 05: Digital Certificates & PKI 📜

A certificate is essentially a "Digital ID Card" that binds a Public Key to an identity (a website or a user).

---

##  What is inside a Digital Certificate?
A certificate contains:
* **Subject Name:** (e.g., google.com)
* **Public Key:** (The website's actual public key)
* **Issuer:** (The CA that signed and verified this certificate)
* **Validity:** (The expiration date)
* **CA's Digital Signature:** (Proof that the CA has verified the identity of the website owner).

##  The Role of PKI (Public Key Infrastructure)
PKI is the framework that manages these certificates:
1. **Issuing:** Creating certificates.
2. **Revoking:** Invalidating certificates if they are stolen or compromised.
3. **Managing:** Tracking validity and expiry.

**Summary:** If the "Digital Signature" is the process, the "Digital Certificate" is the ID card used to facilitate that process. The browser checks the certificate first (to verify the trust chain), extracts the Public Key, and then establishes the secure connection.

---

# 03: Steganography: The Art of Concealment 🕵️‍♂️

Cryptography makes data unreadable, whereas Steganography hides the "existence" of the data entirely.

---

## 1. Steganography vs. Cryptography
* **Cryptography:** "The data is unreadable, but I know it exists." (The lockbox is visible).
* **Steganography:** "I don't even know there is a secret message here." (The message is hidden inside a photo).



## 2. How it works: The LSB Method
The most common technique is the **Least Significant Bit (LSB)** method.
* Images are composed of pixels, and pixels contain color data (RGB).
* Computers store color as bits (0 and 1).
* If you change only the very last bit (the least significant bit) of each pixel, the human eye cannot see the color change, but a computer can store secret data in those bits.

## 3. Types of Steganography
1. **Image Steganography:** Hiding data within pixel data.
2. **Audio Steganography:** Hiding data in frequency patterns within MP3/WAV files.
3. **Appended Data:** Attaching hidden text/files to the very end of a file (detectable via `strings` command).

> **Professional Note:**
> In pentesting, if you encounter an image file with an unusually large file size (e.g., 50MB for a simple icon), suspect hidden data. Always use tools like `strings`, `exiftool`, and `steghide` to investigate files during your capture-the-flag or assessment labs.