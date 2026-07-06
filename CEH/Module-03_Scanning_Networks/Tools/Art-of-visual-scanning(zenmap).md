# Art of Visual Scanning (Zenmap) 🗺️

Welcome to the guide on **Zenmap**. While Nmap is incredibly powerful in the command line (CLI), **Zenmap** is the official Graphical User Interface (GUI) for Nmap. It is designed to make Nmap easier for beginners while adding advanced visual features—like network topology mapping—that even seasoned ethical hackers love for reporting.

---

## 📌 Table of Contents
1. [What is Zenmap?](#-what-is-zenmap)
2. [Why Use Zenmap? (Key Features)](#-why-use-zenmap-key-features)
3. [Pre-Configured Scan Profiles](#-pre-configured-scan-profiles)
4. [The Power of Topology Mapping](#-the-power-of-topology-mapping)
5. [Zenmap vs. Nmap (CLI)](#-zenmap-vs-nmap-cli)

---

## 📖 What is Zenmap?
Zenmap is a multi-platform graphical interface for Nmap. Instead of memorizing dozens of Nmap flags, Zenmap allows you to select options from dropdown menus, automatically builds the command for you, and displays the results in an organized, easy-to-read format.

---

## 🚀 Why Use Zenmap? (Key Features)

Even if you are an Nmap master, Zenmap offers features that the command line simply cannot match visually:
* **Command Wizard:** An interactive tool that helps you build complex Nmap commands step-by-step.
* **Scan Database:** It automatically saves your scan results so you can search and filter through past scans easily.
* **Compare Results:** It has a built-in "diff" tool to compare two different scans (e.g., comparing a network scan from today vs. last week to see if any new ports were opened).
* **Cross-Platform:** Works seamlessly on Linux, Windows, and macOS.

---

## ⚡ Pre-Configured Scan Profiles

Zenmap comes with built-in profiles that automatically apply the right Nmap flags. This is highly useful for quick auditing in CEH labs:

### 1. Intense Scan
The most comprehensive scan. It performs OS detection, version detection, script scanning, and traceroute.
* **Underlying Command:** `nmap -T4 -A -v`
* **Use Case:** When you need to know absolutely everything about a specific target.

### 2. Intense Scan plus UDP
Same as the intense scan, but it also checks UDP ports (which are often ignored).
* **Underlying Command:** `nmap -sS -sU -T4 -A -v`
* **Use Case:** Finding hidden services like DNS (53) or SNMP (161).

### 3. Quick Scan
Scans only the top most common ports to save time.
* **Underlying Command:** `nmap -T4 -F`
* **Use Case:** When you need a rapid overview of a target network.

### 4. Ping Scan
Only checks which hosts are alive without scanning their ports.
* **Underlying Command:** `nmap -sn`
* **Use Case:** Initial Host Discovery / Ping Sweeps.

---

## 🕸️ The Power of Topology Mapping

The absolute best feature of Zenmap is the **Topology Tab**. 
When you run a scan that includes a traceroute (like the Intense Scan), Zenmap automatically generates an interactive, visual map of the network. 

**Why is this important for CEH?**
* **Network Architecture:** It visually reveals how routers, switches, and firewalls are connected between you and the target.
* **Reporting:** Visual maps are highly appreciated in penetration testing reports to show clients their network layout.
* **Color Coding:** Zenmap color-codes hosts (Green = fewer open ports, Red = many open ports/highly vulnerable).

---

## ⚖️ Zenmap vs. Nmap (CLI)

| Feature | Zenmap (GUI) 🗺️ | Nmap (CLI) 💻 |
| :--- | :--- | :--- |
| **Ease of Use** | Beginner friendly, uses dropdowns | Requires memorization of flags |
| **Speed** | Slightly slower due to GUI overhead | Extremely fast and lightweight |
| **Visuals** | Interactive Topology Maps & colored text | Plain text terminal output |
| **Automation** | Hard to automate | Perfect for Bash/Python scripting |
| **Best For** | Learning, mapping, and report generation | Scripting, remote servers (SSH), and quick checks |

> **Pro Tip:** Use the Command Wizard in Zenmap to learn what different flags do. Watch the "Command" bar update in real-time as you click different options, then copy that command into your terminal to practice!

---

⚠️ **Disclaimer:** This document is strictly for educational purposes and official CEH laboratory practice. Unauthorized scanning of networks is punishable under cyber law. Always stick to your sandboxed environments.
