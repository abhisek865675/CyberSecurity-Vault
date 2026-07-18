#  File System Navigation & Operations 🧭

Once you gain access to a Linux system, your first objective is to move around, find sensitive data, and manipulate files to establish persistence or escalate privileges. To do this effectively, you must master how Linux handles file paths and basic operations.

---

## 1. Absolute vs. Relative Paths

Whenever you interact with a file or directory in Linux, you must specify its "path" (its location). There are two ways to do this:

### A. Absolute Path
An absolute path is the full, complete address of a file or directory starting directly from the **Root Directory (`/`)**. 
* **Example:** `/var/www/html/config.php`
* **Rule:** It *always* starts with a forward slash (`/`). No matter where you currently are in the system, an absolute path will always point to the exact same file.

### B. Relative Path
A relative path is the address of a file or directory **relative to your current location**.
* **Example:** If you are currently inside `/var/www/`, and you want to access `config.php` inside the `html` folder, the relative path is simply: `html/config.php`
* **Rule:** It never starts with a forward slash. It assumes you are starting from where you are right now.

> **💡 VAPT Perspective (Path Traversal/LFI):**
> Understanding relative paths is crucial for exploiting Local File Inclusion (LFI) in web applications. The symbols `.` (current directory) and `..` (parent directory) are heavily used. If a web app is vulnerable, a hacker might use the relative path `../../../../etc/passwd` to traverse backward out of the web directory and read system passwords.

---

## 2. Path Detection & Navigation

### `pwd` (Print Working Directory)
When you get a reverse shell, there is often no visual prompt telling you where you are.
* **Command:** `pwd`
* **Output:** `/var/www/html` (Tells you your exact absolute path).

### `cd` (Change Directory)
Used to navigate the file system.
* `cd /tmp` : Moves you to `/tmp` using an absolute path.
* `cd documents` : Moves you into the "documents" folder using a relative path.
* `cd ..` : Moves you one directory up (to the parent folder).
* `cd ~` : Moves you instantly to your user's Home directory.
* `cd -` : Takes you back to the previous directory you were just in (great for quick switching).

---

## 3. Creating Files & Folders

### `mkdir` (Make Directory)
Used to create new folders.
* **Basic:** `mkdir loot` (Creates a folder named "loot").
* **Advanced (`-p`):** `mkdir -p .hidden/payloads/scripts` 
  * *VAPT Note:* The `-p` (parent) flag creates the entire directory tree at once. Starting the folder name with a dot (`.hidden`) makes it invisible to a standard `ls` command, which is great for hiding your exploit scripts from careless system admins.

### `touch` (Create Empty File / Update Timestamps)
* **Create:** `touch exploit.py` (Creates an empty file if it doesn't exist).
* **Timestomping:** If the file already exists, `touch` updates its "last modified" timestamp to the current time. 
  * *VAPT Note:* Advanced attackers use `touch -t YYYYMMDDHHMM file.txt` to forge timestamps (timestomping) to hide their modified backdoor files among old, legitimate system files.

---

## 4. Viewing File Contents

Reading files is 90% of enumeration. You need to know how to read data efficiently.

* **`cat` (Concatenate):** Dumps the entire content of a file onto the screen.
  * *Example:* `cat /etc/passwd`
* **`less`:** Used for very long files. It allows you to scroll up and down using the arrow keys (press `q` to quit).
  * *Example:* `less /var/log/auth.log`
* **`head` / `tail`:** Reads only the first 10 or last 10 lines of a file.
  * *VAPT Note:* `tail -f /var/log/apache2/access.log` is a powerful command. The `-f` (follow) flag keeps the file open and prints new lines in real-time. Pentesters use this to watch server logs live while running web exploits to see how the server reacts.

---

## 5. Basic File Editing

As a pentester, you rarely have a graphical text editor. You must know terminal-based editors to modify configuration files, add SSH keys, or write scripts.

### A. `nano` (The Easy Way)
A simple, user-friendly text editor.
* **Usage:** `nano script.sh`
* **Commands:** Type your text, press `CTRL + O` to save (Write Out), and `CTRL + X` to exit.

### B. `vi` or `vim` (The Hacker's Way)
`vi` is installed on almost *every* Linux system in the world. It is highly powerful but has a steep learning curve.
* **Usage:** `vi config.txt`
* **The Two Modes:** 
  * It opens in **Command Mode** (you cannot type text yet).
  * Press `i` to enter **Insert Mode** (now you can type).
  * Press `ESC` to go back to Command Mode.
* **To Save and Exit:** Press `ESC`, type `:wq`, and hit Enter (Write and Quit).
* **To Exit without Saving:** Press `ESC`, type `:q!`, and hit Enter.

> **💡 VAPT Privilege Escalation Note:**
> If you find out that your low-level user is allowed to run `sudo vi` via the `/etc/sudoers` file, the system is completely compromised. You can open `vi`, type `:!/bin/sh`, and hit Enter. `vi` will spawn a shell with root privileges!

---

## 6. Copying, Moving, and Deleting

### `cp` (Copy)
* **Basic:** `cp original.txt backup.txt`
* **Recursive (`-r`):** `cp -r /var/www/html /tmp/html_backup` (Copies entire directories).
* *OPSEC Tip:* Always use `cp` to back up a configuration file (e.g., `cp sshd_config sshd_config.bak`) before you modify it during a pentest. If you break a production server, you need to be able to restore it immediately.

### `mv` (Move / Rename)
In Linux, moving a file and renaming a file are the exact same operation.
* **Move:** `mv payload.bin /tmp/` (Moves the file into the `/tmp` folder).
* **Rename:** `mv oldname.txt newname.txt` (Renames the file in the current directory).

### `rm` (Remove / Delete)
* **Basic:** `rm file.txt`
* **Folders:** `rm -r foldername` (Recursive delete).
* **Force:** `rm -rf /tmp/loot` (Deletes directories and contents forcefully without asking for confirmation. *Use with extreme caution*).

---
