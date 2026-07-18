#  Users, Groups, & Permissions (The Gateway to Privilege Escalation) 🔐

In Linux, security is heavily reliant on file ownership and permissions. When you gain an initial foothold on a system (usually as a low-privileged user like `www-data` or a standard user), your primary goal is to become `root`. To do this, you must understand exactly how the system decides who is allowed to do what.

---

## 1. Users and Groups (UID & GID)

Linux does not actually care about your username. Under the hood, it identifies users and groups using numbers.

* **UID (User ID):** The unique number assigned to a user.
* **GID (Group ID):** The unique number assigned to a group.

### The Power Levels
* **Root (UID 0):** The god-mode user. If a user has a UID of 0, they are root, regardless of what their actual username is.
* **System Users (UID 1-999):** Service accounts used to run background processes (e.g., `daemon`, `www-data`, `mysql`). They usually cannot log in directly.
* **Standard Users (UID 1000+):** Human users (e.g., `rohit`, `john`). 

### Core Configuration Files
* **`/etc/passwd`:** Contains user info, UID, GID, and their default shell. It is readable by everyone.
  * *VAPT Note:* If you find this file is **writable** by your low-level user, you can inject a new user with UID 0 and an empty password, granting yourself instant root access.
* **`/etc/shadow`:** Contains the actual hashed passwords. It should only be readable by root. 

---

## 2. Standard Permissions (rwx)

When you run `ls -la`, you will see a 10-character string on the left side of every file (e.g., `-rwxr-xr--`). 

* **Character 1 (File Type):** `-` means a normal file, `d` means a directory, `l` means a symlink.
* **Characters 2-10 (Permissions):** Divided into three sets of 3 characters:
  1. **User (u):** What the owner of the file can do.
  2. **Group (g):** What members of the file's group can do.
  3. **Others (o):** What everyone else on the system can do.

| Symbol | Permission | On a File | On a Directory |
| :--- | :--- | :--- | :--- |
| **r** | Read | Can view file contents (`cat`) | Can list files inside (`ls`) |
| **w** | Write | Can modify or delete the file | Can create/delete files inside |
| **x** | Execute | Can run the file as a script/program | Can enter the directory (`cd`) |
| **-** | Denied | Permission missing | Permission missing |

---

## 3. Modifying Permissions (`chmod`)

Administrators and hackers use the `chmod` command to change permissions. There are two ways to do this: the Octal Method (Numbers) and the Symbolic Method (Letters).

### A. The Symbolic Method (The Fast Way)
Instead of doing math, you use letters to define exactly who gets what. This is heavily used during CTFs to quickly make payloads executable.

* **Targets:** `u` (user/owner), `g` (group), `o` (others), `a` (all)
* **Actions:** `+` (add permission), `-` (remove permission), `=` (set exact permission)

**Examples:**
* `chmod u+x payload.sh` (Adds execute permission for the user/owner only).
* `chmod a+r config.txt` (Adds read permission for absolutely everyone).
* `chmod o-w secret.txt` (Removes write permission from 'others').
* `chmod g=rw shared.doc` (Sets group permissions to exactly read/write, removing execute if it existed).

### B. The Octal Method (The Math Way)
Uses numbers to set all permissions at once.
* **Read (r) = 4** | **Write (w) = 2** | **Execute (x) = 1**

**Examples:**
* `chmod 777 file.sh`: (4+2+1, 4+2+1, 4+2+1) -> **`rwxrwxrwx`** (Everyone can read, write, and execute. *A Pentester's dream.*)
* `chmod 644 config.txt`: (4+2, 4, 4) -> **`rw-r--r--`** (Owner can edit, everyone else can only read.)

---

## 4. Modifying Ownership (`chown` & `chgrp`)

Sometimes you need to change who owns a file, especially if you create a backdoor and want to assign it to another compromised user.

* **`chown` (Change Owner):** Changes the primary user who owns the file.
  * `chown rohit payload.py` (Makes 'rohit' the owner).
  * `chown root:root secret.txt` (Changes both the user and the group to root simultaneously).
* **`chgrp` (Change Group):** Changes only the group ownership of a file.
  * `chgrp security_team tools.sh` (Assigns the file to the 'security_team' group).

---

## 5. Advanced Permissions: SUID & SGID (The Privilege Escalation Goldmine)

Standard permissions are not always enough. Sometimes, a normal user needs to do a root-level task. 
*Example:* A normal user needs to change their password using the `passwd` command. Updating a password requires writing to `/etc/shadow`, which only root can do. How is this possible? **SUID**.

### A. SUID (Set Owner User ID)
When the SUID bit is set on an executable file, **it runs with the privileges of the file's owner**, not the person running it.
* If `root` owns a file, and it has the SUID bit set, any normal user who runs that file will temporarily execute it as `root`.
* **How to spot it:** The execute bit (`x`) for the owner is replaced with an **`s`** (e.g., `-rwsr-xr-x`).
* **How to set it:** `chmod u+s binary_file` (This adds the SUID bit to the user).

> **🔥 VAPT Attack Vector (SUID Exploitation):**
> Finding misconfigured SUID binaries is one of the most common ways to get root.
> 1. **Hunt for them:** Run `find / -perm -4000 -type f 2>/dev/null`
> 2. **Exploit them:** If you find a custom script, or a built-in binary like `vim`, `find`, `nmap`, or `python` with the SUID bit set, head over to **GTFOBins** to find the exact command to spawn a root shell.

### B. SGID (Set Group ID)
Similar to SUID, but the file runs with the privileges of the file's **Group**. 
* **How to spot it:** The execute bit for the group is replaced with an **`s`** (e.g., `-rwxr-sr-x`).
* **How to set it:** `chmod g+s directory_name`
* *VAPT Note:* Often used on shared directories. If applied to a folder, any new files created inside will inherit the group ownership of the folder, not the user who created them.

### C. The Sticky Bit
Applied to directories (most commonly `/tmp`). It ensures that only the owner of a file can delete or rename it, even if the directory is world-writable (777). 
* **How to spot it:** The execute bit for 'others' is replaced with a **`t`** (e.g., `drwxrwxrwt`).
* **How to set it:** `chmod +t /shared_folder`
* *VAPT Note:* This is why you can write payloads into `/tmp`, but you cannot delete other users' files in there.



---
