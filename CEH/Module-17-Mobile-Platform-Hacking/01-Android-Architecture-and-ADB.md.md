#  Android Architecture & ADB Basics 📱

To hack Android, you need to understand how the OS is built. Unlike a standard desktop OS, Android is designed as a layered stack where every application runs in a highly restricted, isolated environment called a **Sandbox**.

---

## 📌 Table of Contents
1. [The Android OS Stack](#1-the-android-os-stack)
2. [The Sandbox Mechanism](#2-the-sandbox-mechanism)
3. [ADB (Android Debug Bridge): The Master Key](#3-adb-android-debug-bridge-the-master-key)
4. [Navigating the Android Filesystem](#4-navigating-the-android-filesystem)

---

## 1. The Android OS Stack

Android is built on top of the Linux kernel. Understanding these layers helps you know where you can "hook" into the system.



* **Linux Kernel:** Handles hardware drivers, memory management, and process isolation.
* **Hardware Abstraction Layer (HAL):** Interfaces between the kernel and the higher-level frameworks.
* **Native Libraries (C/C++):** Core components like WebKit (browser engine), SQLite (database), and OpenGL (graphics). This is often where memory corruption vulnerabilities reside.
* **Android Runtime (ART):** Where app bytecode (DEX files) is executed.
* **Application Framework:** The APIs that developers use (e.g., Activity Manager, Content Providers).
* **Apps:** The top layer where you, the user (and the hacker), interact.

---

## 2. The Sandbox Mechanism

Android security is built on the concept of **UID (User ID) Isolation**. 



* Every application is assigned a unique Linux UID at installation.
* By default, an app can *only* access its own data folder (`/data/data/com.package.name/`).
* Apps cannot read data from other apps or the system files. 
* **The Goal of Rooting:** When you "Root" a device, you bypass these UID restrictions, effectively granting yourself UID 0 (Superuser) permissions, allowing you to break out of the sandbox.

---

## 3. ADB (Android Debug Bridge): The Master Key

ADB is a command-line utility that communicates between your computer and the Android device/emulator. Without ADB, you are blind.

### Common ADB Operations:
* **Check connected devices:** `adb devices`
* **Open a shell (command line access to the device):** `adb shell`
* **Install/Uninstall apps:** `adb install app.apk`
* **Pull files from device to PC:** `adb pull /sdcard/data.txt`
* **Push files from PC to device:** `adb push payload.apk /sdcard/`

---

## 4. Navigating the Android Filesystem

Once you are inside `adb shell`, you need to know where to look for interesting data.

| Directory | Purpose |
| :--- | :--- |
| `/data/data/` | **The Honey Pot.** Contains the private data (databases, shared preferences, XML files) for every installed app. |
| `/sdcard/` | The user's external storage. Usually accessible without root. |
| `/system/` | Contains the OS binaries, root certificates, and core configuration. |
| `/data/app/` | Where the installed APK files reside. |

### Essential Shell Commands:
* `run-as com.package.name`: This is a lifesaver. If an app is "debuggable," this command lets you enter the sandbox of that specific app, even without root access!
* `ls -lR`: List everything recursively to find buried configuration files.
* `grep -r "API_KEY" .`: Searching for secrets in the app's internal folders.

---

> **💡 Professional Analysis:**
> If you are testing a production app, the first thing to check is whether the developer set `android:debuggable="true"` in the `AndroidManifest.xml` file. If they did, you don't even need to root the device; you can use `adb shell` and `run-as` to browse their entire internal private database. Always check `AndroidManifest.xml` first!


---
