# 05: Process, Service, & Resource Management ⚙️

As a penetration tester, enumeration is 90% of the work. You need to know exactly what is running on the system to identify potential exploits, backdoors, or defensive monitoring tools.

---

## 1. The Core Lifecycle Concepts

### A. Process
The **basic unit of execution**. A program currently running in the system memory. 
* Every process has a unique **PID (Process ID)**.
* **PPID (Parent Process ID):** The ID of the process that launched it. If the parent dies, the child process is often terminated or re-parented to `systemd` (PID 1).

### B. Daemon
A **background process** that is not attached to any terminal (no user interface).
* They typically start at boot and run until shutdown.
* You can identify them by the 'd' suffix (e.g., `sshd`, `httpd`, `mysqld`).
* They wait for events (like an incoming SSH connection) to trigger their function.

### C. Service
A **management wrapper** for daemons. It is a high-level configuration that ensures a daemon starts correctly, has the right dependencies, and restarts if it crashes.
* We manage these via `systemctl` (in modern Linux).

---

## 2. Process Enumeration Commands

### `ps` (Process Status)
* `ps aux`: The most important command for a VAPT professional.
  * `a`: All users.
  * `u`: Detailed user/owner info.
  * `x`: Background processes (not attached to a terminal).
* **VAPT Note:** Look for processes running as `root` that shouldn't be, or commands in the `COMMAND` column that contain cleartext passwords.

### `top` / `htop`
* Provides a real-time, interactive view of CPU/RAM consumption.
* Great for spotting cryptominers or heavy-duty security monitoring tools that might be tracking your activity.

---

## 3. Managing Jobs (Control & Backgrounding)

If you have a terminal session (shell), you don't want your tools (like a netcat listener) to lock it up.

* **`&` (Backgrounding):** Run any command in the background: `python3 -m http.server 80 &`
* **`CTRL + Z`:** Suspend a foreground process (pauses it).
* **`bg`:** Resume a suspended process in the background.
* **`fg`:** Bring a background process to the foreground.
* **`jobs`:** List all background tasks currently running in your shell.
* **`nohup`:** Prevents a process from dying when you close your SSH/Shell session. `nohup ./backdoor.sh &`

---

## 4. Controlling Processes (The Kill Command)

When you need to stop a process, you send a signal.

* **`kill <PID>` (Signal 15 - SIGTERM):** A polite request to shut down.
* **`kill -9 <PID>` (Signal 9 - SIGKILL):** The "Assassin." Forces an immediate exit. Used when a process is frozen or refuses to stop.
* **`killall <name>`:** Stops all processes matching the name (e.g., `killall apache2`).

---

## 5. Priority Management (The Niceness Value)

Linux handles multitasking by assigning a "Niceness" value (-20 to +19).
* **-20 (Highest Priority):** Egoistic; takes all CPU power.
* **19 (Lowest Priority):** Polite; waits for others.

* **`nice -n <value> <command>`:** Set priority when starting a new process.
* **`renice -n <value> -p <PID>`:** Change priority of a process already running.

---

## 6. Cronjobs: The Persistence Mechanism 🕒

Cronjobs are tasks scheduled to run automatically at specific times. 

* **The Files:**
  * System-wide: `/etc/crontab`
  * User-specific: `crontab -l`
* **The Format:** `Minute Hour Day Month DayOfWeek Command`

### VAPT Perspective:
If you find a script running via cron as `root`, and you have `write` access to it, you can overwrite the script with your own code. When the cronjob triggers, your code will execute with **root privileges**.

**Check for Crons:**
1. `cat /etc/crontab`
2. `ls -la /etc/cron.*`
3. `crontab -l`


---

