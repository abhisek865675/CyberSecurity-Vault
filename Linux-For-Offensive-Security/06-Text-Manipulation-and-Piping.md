
# Text Manipulation, Piping, & Redirection 📜⚡

As a pentester, you will often find yourself staring at massive log files (like `/var/log/auth.log` or Apache access logs) or dumping raw data from commands like `ps aux`. You cannot read these manually. You must use the power of the Linux terminal to filter, parse, and extract exactly the information you need.

---

## 1. Redirection (`>` and `>>`)

Redirection allows you to take the output of a command and save it into a file instead of printing it to your screen.

* **`>` (Overwrite):** Redirects output to a file, **deleting** everything already in that file.
  * *Example:* `ls /home > file_list.txt`
* **`>>` (Append):** Redirects output to a file, **adding** it to the end without deleting existing content.
  * *Example:* `whoami >> user_logs.txt`
* **`2> /dev/null` (Suppress Errors):** In Linux, error messages are sent to "Standard Error" (2). If you run a command like `find / -name flag.txt` and get 1,000 "Permission Denied" errors, use `find / -name flag.txt 2>/dev/null` to hide the errors and only see the results.

---

## 2. Piping (`|`)

The pipe symbol is the most powerful feature in the Linux terminal. It takes the **output** of the command on the left and sends it as the **input** to the command on the right.

* *Concept:* `Command A | Command B | Command C`
* *Example:* `ps aux | grep "apache2"` (Get all processes, then pipe that list to grep to find only the lines containing "apache2").

---

## 3. The Power Trio: `grep`, `awk`, and `sed`

### A. `grep` (The Searcher)
Used to find specific patterns or keywords within files or command outputs.
* `grep "root" /etc/passwd` : Finds all lines containing "root".
* `grep -i "error" /var/log/syslog` : The `-i` flag makes it **case-insensitive** (finds "Error", "ERROR", "error").
* `grep -r "API_KEY" .` : The `-r` (recursive) flag searches every single file in the current folder and subfolders for the string "API_KEY".

### B. `awk` (The Data Extractor)
`awk` is basically a programming language inside your terminal. It is excellent for extracting specific "columns" of data.
* *Example:* Look at the output of `ls -l`.
* `ls -l | awk '{print $3}'` : This prints only the 3rd column (which is the Owner of the file).
* *VAPT Use Case:* Parsing large output from tools like `nmap` or `netstat` to get just the IP addresses or Port numbers.

### C. `sed` (The Stream Editor)
`sed` is used to find and replace text in files without opening them.
* `sed -i 's/oldtext/newtext/g' file.txt`
* **`s`**: Substitute.
* **`g`**: Global (replace all occurrences in a line).
* **`i`**: In-place (save the changes directly to the file).
* *VAPT Use Case:* If you successfully inject a reverse shell command into a configuration file, you can use `sed` to fix syntax errors remotely without needing a text editor.

---

## 4. Practical VAPT Workflow Example 🕵️‍♂️

Imagine you want to see if any user is currently logged in, then find their process IDs, and finally save them to a file:

1. **Get logged-in users:** `w`
2. **Find all processes owned by those users:** `ps aux | grep "username"`
3. **Extract only the PIDs (2nd column):** `ps aux | grep "username" | awk '{print $2}'`
4. **Save to a file for later analysis:** `ps aux | grep "username" | awk '{print $2}' > suspicious_pids.txt`

---

## 5. Sorting and Unique Values

* **`sort`:** Sorts the output alphabetically or numerically.
* **`uniq`:** Filters out duplicate lines (the file must be sorted first for this to work correctly).
* *Combined Pentest Command:* `cat access.log | awk '{print $1}' | sort | uniq -c`
  * This counts how many times each unique IP address has connected to your server. Extremely useful for identifying **Brute Force** attacks (an IP address appearing 5,000 times in 1 minute).

> **💡 VAPT Tip:**
> When you are on a restricted machine (a "shell"), you don't have Burp Suite or a GUI. You have to be a master of `grep`, `awk`, and `sed`. If you can parse logs quickly, you can spot the attacker's activities or find sensitive credentials faster than the system admin can stop you.


---
