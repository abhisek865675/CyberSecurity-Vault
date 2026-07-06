# Metasploit Architecture & Components 🏗️

To effectively use Metasploit, you must understand what is happening under the hood. Metasploit is not just a collection of scripts; it is a sophisticated, modular framework built on a foundation of Ruby libraries.

---

## 📌 Table of Contents
1. [The Framework Architecture](#-the-framework-architecture)
2. [Primary Modules](#-primary-modules)
3. [Understanding Payloads (Crucial)](#-understanding-payloads)
4. [Interfaces](#-interfaces)

---

## 🏗️ The Framework Architecture
The Metasploit Framework is designed in layers. At the bottom, you have **Rex** (the basic library that handles sockets, protocols, and transformations). Above that is the **Core** library, and finally, the **Base** library which provides the API for the actual modules.



---

## 🧱 Primary Modules
Modules are the individual "tools" or "plugins" inside Metasploit. Each has a specific role:

* **Exploits (`/modules/exploits`):**
  Code that leverages a specific vulnerability (like a Buffer Overflow) to gain unauthorized access to a target system.
* **Auxiliary (`/modules/auxiliary`):**
  Modules that *do not* exploit anything but are essential for the attack cycle. This includes port scanners, FTP/SMB/HTTP enumerators, and fuzzers.
* **Encoders (`/modules/encoders`):**
  Tools used to reformat your payload so it evades Antivirus (AV) and Intrusion Detection Systems (IDS). They "scramble" the signature of the malicious code.
* **Post (`/modules/post`):**
  Modules designed to run *after* you have successfully gained access (Post-Exploitation). Examples include dumping password hashes, pivoting through a network, or downloading files.
* **NOPs (`/modules/nops`):**
  "No Operation" generators. These are used to create padding in buffer overflow attacks to keep the payload size stable.

---

## 📦 Understanding Payloads (Crucial for CEH)
A Payload is the code that runs on the target system *after* the exploit succeeds. Metasploit categorizes payloads into three specific types:

| Payload Type | Description |
| :--- | :--- |
| **Singles** | Standalone payloads (e.g., a simple command like `adduser`). They are self-contained and small. |
| **Stagers** | Tiny pieces of code that establish a network connection between the attacker and the victim. Their only job is to download the rest of the payload. |
| **Stages** | Large components (like **Meterpreter**) that are downloaded by the Stager. They provide advanced features like screen capture, webcam access, and file management. |

### Meterpreter: The Gold Standard
Meterpreter is a "Stage" payload. It lives entirely in the **memory** of the target machine (it never touches the disk), making it extremely difficult for Antivirus software to detect.

---

## 🖥️ Interfaces
How do you interact with these modules?

1. **`msfconsole`:** The most popular interface. It is the centralized, all-in-one terminal for interacting with the framework.
2. **`msfvenom`:** A combination of two legacy tools (`msfpayload` and `msfencode`). This is used specifically to **generate** standalone malicious files (like `.exe`, `.apk`, or `.php` shells) that you can send to a target.
3. **`Armitage`:** A visual, GUI-based front-end for Metasploit, useful for visualizing network mapping and managing multiple exploits simultaneously.

---

> **💡 Quick Tip for the Exam:**
> Remember the distinction: **Exploits** get you in, **Payloads** execute your commands, and **Encoders** help you stay hidden.


---
