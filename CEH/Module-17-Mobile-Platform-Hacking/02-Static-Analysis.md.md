#  Static Analysis & Reverse Engineering 🔍

Static analysis is the process of examining an application's code and resources without actually running it. This is your "reconnaissance" phase. Before you fire up the app, you want to know what secrets the developer hardcoded into it.



---

## 📌 Table of Contents
1. [The Static Analysis Workflow](#1-the-static-analysis-workflow)
2. [Essential Toolchain](#2-essential-toolchain)
3. [The Anatomy of an APK](#3-the-anatomy-of-an-apk)
4. [What to Hunt For?](#4-what-to-hunt-for)

---

## 1. The Static Analysis Workflow

Static analysis follows a simple pattern:
1. **Unpacking:** Breaking the APK (which is essentially a ZIP file) into its constituent resources and bytecode.
2. **Decompilation:** Converting the machine-readable DEX (Dalvik Executable) code back into human-readable Java/Kotlin code.
3. **Audit:** Searching the code for security flaws, configuration issues, and hardcoded secrets.

---

## 2. Essential Toolchain

* **APKTool:** The "Swiss Army Knife" for unpacking and repacking APKs. It extracts the `AndroidManifest.xml`, layout files, and resources.
* **JADX-GUI:** The best tool for decompilation. It takes an APK and transforms the bytecode into clean, readable Java source code.
* **Strings (Command line):** A simple but effective Linux utility to extract plain text snippets from binary files. Sometimes, API keys are sitting in plain sight.

### Basic Commands:
```bash
# Decompile the APK using APKTool
apktool d target_app.apk

# For JADX, simply open the APK in the GUI
jadx-gui target_app.apk
```

## 3. The Anatomy of an APK

When you decompile an app, look for these specific files first:

- **`AndroidManifest.xml`:** This is the heart of the app. It lists every permission the app requests, every activity (screen), and whether the app is "debuggable."
    
- **`res/` directory:** Contains images, layouts, and string constants. You will often find hardcoded error messages or API URLs here.
    
- **`classes.dex`:** This contains the actual logic of the app. This is what JADX-GUI transforms into Java code.
    
- **`assets/`:** Developers often hide large files here, including custom configuration files, databases, or even embedded keys.
    

## 4. What to Hunt For?

When you are reading the decompiled source code, keep your eyes peeled for:

1. **Hardcoded Secrets:** API keys (AWS, Google Maps, Firebase), hardcoded encryption keys, or cleartext passwords.
    
2. **Insecure API Endpoints:** Look for URLs in the code. Often, developers leave staging or debug URLs (e.g., `https://dev-api.target.com`) that are less secure than the production endpoints.
    
3. **Dangerous Permissions:** In the `AndroidManifest.xml`, check for permissions like `android.permission.READ_EXTERNAL_STORAGE` or `android.permission.ACCESS_FINE_LOCATION` that might be unnecessary for the app's functionality.
    
4. **Weak Crypto:** Look for code that uses `MD5` or `SHA1` for passwords, or worse, code that hardcodes the IV (Initialization Vector) for AES encryption—this makes the encryption totally breakable.
    

> **💡 Professional Analysis:** Static analysis is 80% about search skills. Once you open the code in JADX-GUI, use the "Search" function (Ctrl+Shift+F) to look for keywords like: `key`, `secret`, `password`, `token`, `https`, `http`, `admin`, and `login`. 9 out of 10 times, you will find something interesting just by searching for these terms across the entire project.


---
