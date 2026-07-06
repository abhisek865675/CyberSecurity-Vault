#  Dynamic Analysis & Frida Instrumentation 🪝

Static analysis is about reading the map; dynamic analysis is about changing the terrain while you are driving through it. By using dynamic instrumentation, you can modify an application's behavior while it is running, effectively bypassing security controls like Root Detection or SSL Pinning.



---

## 📌 Table of Contents
1. [The Dynamic Advantage](#1-the-dynamic-advantage)
2. [What is Frida?](#2-what-is-frida)
3. [Common Use Cases](#3-common-use-cases)
4. [Using Objection for Rapid Exploration](#4-using-objection-for-rapid-exploration)

---

## 1. The Dynamic Advantage

Why do we need dynamic analysis if we already have the source code?
* **Obfuscation:** Many apps use ProGuard or R8 to scramble code, making it unreadable.
* **Complex Logic:** Some security checks are computed dynamically based on the current state of the device.
* **SSL Pinning:** Apps often refuse to communicate with Burp Suite (man-in-the-middle) because they verify the server certificate against a hardcoded copy. Static analysis can't easily turn off this network-level enforcement.

---

## 2. What is Frida?

Frida is a dynamic instrumentation toolkit. It allows you to inject your own JavaScript code into the process space of a running application.

* **Hooking:** You can "hook" any function. When the app calls a specific method (like `isRooted()`), your code runs first, executes your logic, and forces the method to return `false`.
* **Cross-Platform:** Works on Android, iOS, Windows, and Linux.

### Basic Frida Hook Example:
If you want to force a function `checkPassword()` to always return `true`:

```javascript
Java.perform(function () {
    var myClass = Java.use("com.example.app.SecurityHelper");
    myClass.checkPassword.implementation = function () {
        console.log("Password check intercepted! Forcing true.");
        return true; // Always return true
    };
}); 
```

## 3. Common Use Cases

|**Vulnerability**|**Dynamic Technique**|
|---|---|
|**Root Detection**|Hook the method checking for `su` binaries or `Test-keys` and force it to return `false`.|
|**SSL Pinning**|Hook the certificate validation function to return `true` or bypass the verification entirely.|
|**Integrity Checks**|Bypass checksum validation that prevents you from modifying the APK.|
|**Parameter Tampering**|Intercept network-related functions to modify data before it is encrypted/sent to the server.|

## 4. Using Objection for Rapid Exploration

Writing Frida scripts from scratch is slow. **Objection** is a runtime exploration toolkit built on top of Frida that gives you a CLI to explore apps without writing complex scripts.

### Common Objection Commands:

Bash

``` bash
# Explore the app's internal filesystem
objection --gadget "com.package.name" explore
> ls
> env

# Bypass SSL Pinning with one command
> android sslpinning disable

# Bypass Root Detection
> android root disable
```

> **💡 Professional Analysis:**
> 
> Dynamic analysis is the "God Mode" of mobile hacking. The most critical skill to develop here is **Method Tracing**. Before you write a hook, use Objection or Frida to list all classes and methods. If you are targeting a login bypass, filter for methods containing the strings `Auth`, `Login`, or `Token`. Often, the method names are self-explanatory, and you can hook them within minutes to see exactly what arguments are being passed to them.


---
