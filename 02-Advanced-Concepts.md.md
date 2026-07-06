# Advanced Concepts: Certificates & Signatures 📜

## 1. Digital Signatures
A digital signature ensures that a message was actually sent by the claimed sender (Non-repudiation) and hasn't been tampered with (Integrity).
* **Creation:** Sender uses their **Private Key** to sign a hash of the data.
* **Verification:** Receiver uses the Sender's **Public Key** to verify.



## 2. Certificates (PKI)
How do you know that a "Public Key" actually belongs to the person claiming it? You use a **Digital Certificate** signed by a trusted third party called a **Certificate Authority (CA)**. This is the foundation of HTTPS/SSL.

## 3. Steganography
The art of hiding secret data inside non-secret files (images, audio, video).
* **Goal:** Concealment (nobody should know the message is there).
* **Technique:** Least Significant Bit (LSB) method (modifying the color values of pixels).